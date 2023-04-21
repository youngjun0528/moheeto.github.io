---
title: "01 - Serverless App. Test in AWS"
description: 
published: true
date: 2021-10-19T05:06:51.256Z
tags: 
- Cloud
- CI/CD
- Jenkins
- Build
- Test
editor: markdown
dateCreated: 2021-10-19T01:04:35.918Z
categories: 
- jenkins
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "01 - Serverless App. Test in AWS"
---

# 테스트 분류
## 단위 테스트 (Unit tests)
격리된 단일 아키텍처 구성 요소에 대한 코드에 대해 실행되는 테스트
- i.e.) 각 소스의 단위 테스트 케이스
## 통합 테스트 (Integration tests)
일반적으로 클라우드 환경에서 둘 이상의 아키텍처 구성 요소에 대해 실행
- i.e.) Jenkins에서 수행하는 통합 테스트 케이스
## 엔드 투 엔드 테스트 (End-to-end tests)
전체 애플리케이션에서 동작을 확인
- i.e.) 3자 통합 테스트

# 테스트 도구
## 에뮬레이터 (Emulators)
리소스를 프로비저닝하거나 호출하지 않고 클라우드 서비스를 모방하도록 설계된 애플리케이션
ex.) LocalStack: https://github.com/localstack/localstack
## Mocks (Fakes)
종속성을 해당 종속성의 시뮬레이션으로 대체하는 테스트 애플리케이션의 구현
ex.) Moto: https://github.com/getmoto/moto

# 테스트 목표
## 소프트웨어 품질 향상
모든 코드를 구현하고, 외부 서비스와 연결되는 구성요소를 모두 테스트를 하여야 하는 서버 기반 아키텍쳐에 비해서
서버리스 아키텍쳐에서는 AWs Lambda 와 같이 작은 작업단위가 구성되고, 
대부분의 기능은 S3 혹은 SQS 와 같이 관리형 서비스에 위임하여 처리를 하므로 이를 포괄하는 테스트 전략이 필요
## 기능 구현 및 변경 시간 단축
서버리스 아키텍쳐는 관리형 서비스와의 API 호출을 통해 구현되어 있으므로,
사용자 환경과 배포 환경과의 차이가 발생할 수 있다. 따라서 이러한 서비스와의 상호작용의 결과를 일관성 있게 보여주는 테스트 전략이 필요.

# 테스트 기법
## 모의 테스트
코드에서 클라우드 서비스 동작을 시뮬레이션 하는 대체 객체를 만드는 전략
### 장점
- API 및 SaaS 제공업체와 같이 타사 서비스에 직접 접근할 필요 없이 시뮬레이션
- 장애 조건을 테스트 하기 용이함
- 빠른 로컬 개발 환경을 제공할 수 있음. (서비스 접근 권한 부여 등 필요 없음.)
- 새로운 기능이나 동작이 출시되면 업데이트된 새 기능을 Mock 에 적용하여 빠르게 적용 테스트 할 수 있음.
### 단점
- 많은 양의 사전 설정 및 구성 작업이 필요(특히 서비스 반환 값을 설정하는데 사전 확인이 필요함.)
- 모의 객체는 개발자의 책임이 뒤따른다.
- API의 정확한 이해를 위해서는 서비스에 직접 접근 혹은 호출이 필요하다.
- 로컬 환경에서는 통과하지만 클라우드 환경에서는 실패할 가능성이 있음.
- 권한 부여 혹은 할당량 테스트는 불가능 (실제 클라우드 환경에서의 결과를 예측하기 어려움.)

![cloud-serverless-test-01-01.jpg](..%2F..%2Fassets%2Fimg%2Fcloud%2Fcloud-serverless-test-01-01.jpg)


## 에뮬레이션 테스트
AWS 서비스와 유사한 에뮬레이터를 구현하여 로컬에서 실행되는 AWS 서비스
### 장점
- 로컬 환경에 AWS 서비스 및 RDS 환경을 구축하여 빠르고 격리된 로컬 테스트 기능을 구현할 수 있다.
- 실제 클라우드 인프라를 변경할 필요가 없으므로 기존 테스트 패턴으로 구현이 가능하다. (별도의 Mock, Stub 환경을 구현할 필요가 없다.)
### 단점
- 에뮬레이터의 기능은 제공된 에뮬레이터 서비스 회사에 종속되어 빠른 피드백이 어렵다.
- 에뮬레이터 환경 복제가 어려움. 즉, 다른 개발자에게 에뮬레이터 환경 구축이 어려울 수 있으며, CI/CD 환경에서 구성하기 어렵다.
- 권한 부여 혹은 할당량 테스트는 불가능 (실제 클라우드 환경에서의 결과를 예측하기 어려움.)
- 에뮬레이터가 제공하지 못하는 서비스는 테스트가 불가능하다.

![cloud-serverless-test-01-02.jpg](..%2F..%2Fassets%2Fimg%2Fcloud%2Fcloud-serverless-test-01-02.jpg)

## 클라우드 테스트
클라우드 환경에 배포된 코드에 대해서 테스트를 실행
### 장점
- 최신 API 와 반환값으로 테스트를 할 수 있다.
- 테스트에는 IAM 정책, 서비스 할당량, 구성 및 모든 서비스가 포함
- 클라우드 테스트 환경은 운영 런타임 환경과 가장 유사하므로 클라우드에서 실행하는 테스트는 환경을 통해 진행되면서 가장 일관된 결과
### 단점
- 배포시 많은 시간이 소요된다.
- 서비스 사용 비용이 추가된다.
- 인터넷에 접근이 불가능한 환경이라면 테스트가 불가능하다.
- 격리된 클라우드 환경이 필요하다.
  - 보통 OP 운영환경 / QA 테스트 환경 / ST 개발 환경으로 분리되어 있으나, 
  - 타 시스템과 종속적인 관계라면 ST 환경이라도 잦은 변경은 타 부서의 반발을 감수하여야 한다.

![cloud-serverless-test-01-03.jpg](..%2F..%2Fassets%2Fimg%2Fcloud%2Fcloud-serverless-test-01-03.jpg)
  
## 모범 사례
### 클라우드에서의 테스트 우선 순의 지정
- 단위 테스트, 통합 테스트 및 end-to-end 테스트를 비롯한 테스트의 모든 단계에서 유용
- 격리된 환경에 액세스할 수 있어야 합니다. 
  - ST, QA, OP 환경은 서로 분리 독립되어야 하며, 상호 간의 간섭이 일어나서는 안된다.
  - 이상적으로는 동일한 코드로 작업하는 여러 개발자가 동일한 이름을 가진 리소스에 대해 API 호출을 배포하거나 호출하려고 할 때 발생할 수 있는 리소스 이름 지정 문제를 방지하기 위해 각 개발자에게 전용 AWS 계정이 필요하다.
  - 다른 개발자와 단일 AWS 계정을 공유해야 하는 경우 자동화된 테스트 프로세스에서 각 개발자마다 고유한 리소스 이름을 지정
- 가급적 클라우드에서 테스트하는 데 집중하는 것이 운영 환경의 안정성으로 다양한 요구 사항 및 조건을 충족
- 테스트 혹은 운영 환경 배포시 통합 테스트 및 End-to-End 반드시 테스트가 진행되어야 하며, 이는 모의 객체가 아닌 클라우드 테스트를 통하여 진행한다.

### 필요한 경우 모의 객체 사용
- 복잡한 내부 비지니스 로직을 테스트 할 때에 유용
- 입력 변수에 대한 변형이 일어나는 경우를 테스트 
  - Python 의 경우 정적분석은 pylint 로 확인 가능하고, Type Hint 를 통해 입력 변수를 제어할 수 있으나,
  - pylint 확인할 수 없는 입력 변수의 오류, 객체 리터널에 대한 감시가 필요한 경우 모의 객체를 사용할 수 있다.
- 다른 클라우드 서비스에 의존적이지 않은 테스트를 하고자 할 때에 필요하다.
  - 운영 하고자 하는 서비스에서는 정상이지만 연관되어 있는 다른 클라우드 서비스 때문에 실패하는 경우 실패 원인을 명확히 하기 위해서 필요
- 많은 테스트를 주기적으로 자주 테스트를 하는 경우 모의 객체를 사용하여 테스트 하는 것도 좋은 방법

### 에뮬레이터에서의 테스트는 가급적 회피
- 에뮬레이터의 기능이 충분히 충족하는 지 확인이 반드시 필요하다.
- 에뮬레이터의 사용법은 초기 개발자 투입시 적절한 내부 리소스를 활용하여 구조 파악에 용이할 수 있으므로 제한된 환경에서 사용하도록 한다.
- 모든 개발자 환경에서 에뮬레이터를 설치할 수 있는지 가이드가 필요하다.
  - 가급적 Docker 형태로 전달해 주면 유용할 것으로 판단된다.

## Python UnitTest Sample
https://github.com/aws-samples/serverless-test-samples/tree/main/python-test-samples
- AWS Lambda 서비스 구성도
![cloud-serverless-test-01-05.png](..%2F..%2Fassets%2Fimg%2Fcloud%2Fcloud-serverless-test-01-05.png)
1) AWS Lambda Trigger
2) DynamoDB 내용을 조회하고 조회 결과를 S3에 저장하는 기능
3) DynamoDB는 DataType 과 Customer ID 가 저장
4) S3 에 DataType 과 Customer ID 를 저장하고 객체 S3 Obj Key를 리턴
![cloud-serverless-test-01-06.png](..%2F..%2Fassets%2Fimg%2Fcloud%2Fcloud-serverless-test-01-06.png)


```Python
# Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
# SPDX-License-Identifier: MIT-0
# Start of unit test code: tests/unit/src/test_sample_lambda.py

import sys
import os
import json
from unittest import TestCase
from unittest.mock import MagicMock, patch
from boto3 import resource, client
import moto
from aws_lambda_powertools.utilities.validation import validate

# [0] Import the Globals, Classes, Schemas, and Functions from the Lambda Handler
sys.path.append('./src/sample_lambda')
from src.sample_lambda.app import LambdaDynamoDBClass, LambdaS3Class   # pylint: disable=wrong-import-position
from src.sample_lambda.app import lambda_handler, create_letter_in_s3  # pylint: disable=wrong-import-position
from src.sample_lambda.schemas import INPUT_SCHEMA                     # pylint: disable=wrong-import-position

# [1] Mock all AWS Services in use
@moto.mock_dynamodb
@moto.mock_s3
class TestSampleLambda(TestCase):
    """
    Test class for the application sample AWS Lambda Function
    """

    # Test Setup
    def setUp(self) -> None:
        """
        Create mocked resources for use during tests
        """

        # [2] Mock environment & override resources
        self.test_ddb_table_name = "unit_test_ddb"
        self.test_s3_bucket_name = "unit_test_s3_bucket"
        os.environ["DYNAMODB_TABLE_NAME"] = self.test_ddb_table_name
        os.environ["S3_BUCKET_NAME"] = self.test_s3_bucket_name

        # [3a] Set up the services: construct a (mocked!) DynamoDB table
        dynamodb = resource("dynamodb", region_name="us-east-1")
        dynamodb.create_table(
            TableName = self.test_ddb_table_name,
            KeySchema=[{"AttributeName": "PK", "KeyType": "HASH"}],
            AttributeDefinitions=[{"AttributeName": "PK", "AttributeType": "S"}],
            BillingMode='PAY_PER_REQUEST'
            )

        # [3b] Set up the services: construct a (mocked!) S3 Bucket table
        s3_client = client('s3', region_name="us-east-1")
        s3_client.create_bucket(Bucket = self.test_s3_bucket_name )

        # [4] Establish the "GLOBAL" environment for use in tests.
        mocked_dynamodb_resource = resource("dynamodb")
        mocked_s3_resource = resource("s3")
        mocked_dynamodb_resource = { "resource" : resource('dynamodb'),
                                     "table_name" : self.test_ddb_table_name  }
        mocked_s3_resource = { "resource" : resource('s3'),
                               "bucket_name" : self.test_s3_bucket_name }
        self.mocked_dynamodb_class = LambdaDynamoDBClass(mocked_dynamodb_resource)
        self.mocked_s3_class = LambdaS3Class(mocked_s3_resource)


    def test_create_letter_in_s3(self) -> None:
        """
        Verify given correct parameters, the document will be written to S3 with proper contents.
        """

        # [5] Post test items to a mocked database
        self.mocked_dynamodb_class.table.put_item(Item={"PK":"D#UnitTestDoc",
                                                        "data":"Unit Test Doc Corpi"})
        self.mocked_dynamodb_class.table.put_item(Item={"PK":"C#UnitTestCust",
                                                        "data":"Unit Test Customer"})

        # [6] Run DynamoDB to S3 file function
        test_return_value = create_letter_in_s3(
                        dynamo_db = self.mocked_dynamodb_class,
                        s3=self.mocked_s3_class,
                        doc_type = "UnitTestDoc",
                        cust_id = "UnitTestCust"
                        )

        # [7] Ensure the data was written to S3 correctly, with correct contents
        bucket_key = "UnitTestCust/UnitTestDoc.txt"
        body = self.mocked_s3_class.bucket.Object(bucket_key).get()['Body'].read()

        # Test
        self.assertEqual(test_return_value["statusCode"], 200)
        self.assertIn("UnitTestCust/UnitTestDoc.txt", test_return_value["body"])
        self.assertEqual(body.decode('ascii'),"Dear Unit Test Customer;\nUnit Test Doc Corpi")


    def test_create_letter_in_s3_doc_type_notfound_404(self) -> None:
        """
        Verify given a document type not present in the data table, a 404 error is returned.
        """
        # [8] Post test items to a mocked database
        self.mocked_dynamodb_class.table.put_item(Item={"PK":"D#UnitTestDoc",
                                                        "data":"Unit Test Doc Corpi"})
        self.mocked_dynamodb_class.table.put_item(Item={"PK":"C#UnitTestCust",
                                                        "data":"Unit Test Customer"})

        # [9] Run DynamoDB to S3 file function
        test_return_value = create_letter_in_s3(
                            dynamo_db = self.mocked_dynamodb_class,
                            s3=self.mocked_s3_class,
                            doc_type = "NOTVALID",
                            cust_id = "UnitTestCust"
                            )

        # Test
        self.assertEqual(test_return_value["statusCode"], 404)
        self.assertIn("Not Found", test_return_value["body"])

    def test_create_letter_in_s3_customer_notfound_404(self) -> None:
        """
        Verify given a user id not present in the data table, a 404 error is returned.
        """

        # [10] Post test items to a mocked database
        self.mocked_dynamodb_class.table.put_item(Item={"PK":"D#UnitTestDoc",
                                                        "data":"Unit Test Doc Corpi"})
        self.mocked_dynamodb_class.table.put_item(Item={"PK":"C#UnitTestCust",
                                                        "data":"Unit Test Customer"})

        # [11] Run DynamoDB to S3 file function
        test_return_value = create_letter_in_s3(
                            dynamo_db = self.mocked_dynamodb_class,
                            s3=self.mocked_s3_class,
                            doc_type = "UnitTestDoc",
                            cust_id = "NOTVALID"
                            )

        # Test
        self.assertEqual(test_return_value["statusCode"], 404)
        self.assertIn("Not Found", test_return_value["body"])

    # [12] Load and validate test events from the file system
    def load_sample_event_from_file(self, test_event_file_name: str) ->  dict:
        """
        Loads and validate test events from the file system
        """
        event_file_name = f"tests/events/{test_event_file_name}.json"
        with open(event_file_name, "r", encoding='UTF-8') as file_handle:
            event = json.load(file_handle)
            validate(event=event, schema=INPUT_SCHEMA)
            return event

    # [13] Patch the Global Class and any function calls
    @patch("src.sample_lambda.app.LambdaDynamoDBClass")
    @patch("src.sample_lambda.app.LambdaS3Class")
    @patch("src.sample_lambda.app.create_letter_in_s3")
    def test_lambda_handler_valid_event_returns_200(self,
                            patch_create_letter_in_s3 : MagicMock,
                            patch_lambda_s3_class : MagicMock,
                            patch_lambda_dynamodb_class : MagicMock
                            ):
        """
        Verify the event is parsed, AWS resources are passed, the 
        create_letter_in_s3 function is called, and a 200 is returned.
        """

        # [14] Test setup - Return a mock for the global variables and resources
        patch_lambda_dynamodb_class.return_value = self.mocked_dynamodb_class
        patch_lambda_s3_class.return_value = self.mocked_s3_class

        return_value_200 = {"statusCode" : 200, "body":"OK"}
        patch_create_letter_in_s3.return_value = return_value_200

        # [15] Run Test using a test event from /tests/events/*.json
        test_event = self.load_sample_event_from_file("sampleEvent1")
        test_return_value = lambda_handler(event=test_event, context=None)

        # [16] Validate the function was called with the mocked globals
        # and event values
        patch_create_letter_in_s3.assert_called_once_with(
                                        dynamo_db=self.mocked_dynamodb_class,
                                        s3=self.mocked_s3_class,
                                        doc_type=test_event["pathParameters"]["docType"],
                                        cust_id=test_event["pathParameters"]["customerId"])

        self.assertEqual(test_return_value, return_value_200)


    def tearDown(self) -> None:

        # [13] Remove (mocked!) S3 Objects and Bucket
        s3_resource = resource("s3",region_name="us-east-1")
        s3_bucket = s3_resource.Bucket( self.test_s3_bucket_name )
        for key in s3_bucket.objects.all():
            key.delete()
        s3_bucket.delete()

        # [14] Remove (mocked!) DynamoDB Table
        dynamodb_resource = client("dynamodb", region_name="us-east-1")
        dynamodb_resource.delete_table(TableName = self.test_ddb_table_name )

# End of unit test code
```

# 적용 사례
## T20-Common 적용 사례
### JenKins Build 자동화 With UnitTest in Docker
### Jenkins Integration 테스트 자동화 ( Blue Green ? )


