---
title: "01 - AWS ECS Jenkins Build"
description: 
published: true
date: 2021-10-19T05:06:51.256Z
tags: 
- cloud
- CI/CD
- Jenkins
- Build
editor: markdown
dateCreated: 2021-10-19T01:04:35.918Z
categories: 
- jenkins
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "01 - AWS ECS Jenkins Build"
---

# AWS ECS Jenkins Pipeline Build
![cloud-jenkins-build.jpg](/assets/img/personal/cloud-jenkins-build.jpg)

```javascript
pipeline {
	agent any
  
	stages {
  	stage('Source Build') {
    	steps {
      	script {
          try{
            dir({WORKSPACE}){
              sh "mvn -P deploy clean compile package"
            }
          } catch (err) {
            throw err
          }
        }
      }
    }
    stage('Upload'){
      steps {
        script{
          try{
            withAWS(credentials:"AWS_KEY", region:"{AWS_REGION}"){
            	// source
              sh "aws s3 cp SOURCE TARGET"
              // appspec.yaml
              sh "aws s3 cp SOURCE TARGET"
            }
          } catch (err) {
            throw err
          }
        }
      }
    }
    stage('Docker Build'){
      steps {
        script{
          sh "docker build"
        }
      }
    }
    stage('ECR Push'){
      steps {
        script{
          try{
            withAWS(credentials:"AWS_KEY", region:"{AWS_REGION}"){
              script{
                def ecr_login = ecrLogin(email:false, registryIds:["${AWS_ACCOUNT_ID}"])
                sh "${ecr_login}"
                sh "docker push IMAGE_NAME:IMAGE_TAG"
              }
            }
          } catch (err) {
            throw err
          }
        }
      }
    }
    stage('Auto Scaling'){
      steps {
        script{
          try{
            withAWS(credentials:"AWS_KEY", region:"{AWS_REGION}"){
              script{
                sh "aws autoscaling set-desired-capacity --auto-scaling-group-name ${GROUP_NAME} --desired-capacity ${NUM}"
                sleep(30)
                while(true){
                  def checkEC2State = sh (
                    script: "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name ${GROUP_NAME} --query AutoScalingGroups[0].Instances[*].LifecycleState",
                    returnStdout: true,
                    encoding: "UTF-8"
                  ).trim()
                  if(!checkEC2State.contains("Panding")){
                    break
                  }
                }
              }
            }
          } catch (err) {
            throw err
          }
        }
      }
    }
    stage('CodeDeploy'){
      steps {
        script{
          try{
            withAWS(credentials:"AWS_KEY", region:"{AWS_REGION}"){
              script{
                def create_deployid = sh(
                  script: "aws deploy create-deployment --cli-input-json ${DEPLOY_JSON}",
                  returnStdout: true
                ).trim()
                def DEPLOYMENT_ID = readJSON text: create_deployid
                def DEPLOYMENT_RESULT = ""
                while("$DEPLOYMENT_RESULT" != "\"Succeeded\"") {
                  DEPLOYMENT_RESULT = sh(
                      script:"aws deploy get-deployment --query deploymentInfo.status --region {AWS_REGION} --deployment-id ${DEPLOYMENT_ID.deploymentId}",
                      returnStdout: true
                  ).trim()
                  if ("$DEPLOYMENT_RESULT" == "\"Failed\"") {
                    throw new Exception("CodeDeploy Failed")
                    break
                  }
                  sleep(15)
                }
              }
            }
          } catch (err) {
            throw err
          }
        }
      }
    }
    stage('Rooling'){
      steps {
        script{
          try{
            withAWS(credentials:"AWS_KEY", region:"{AWS_REGION}"){
              script{
                sh "aws ecs update-service --cluster ${CLUSTER} --service ${SERVICE} --desired-count 1 --force-new-deployment"
              }
            }
          } catch (err) {
            throw err
          }
        }
      }
    }
  }
}
```