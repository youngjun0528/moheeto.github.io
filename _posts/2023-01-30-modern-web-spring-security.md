---
title: "Spring Security"
description: 
published: true
date: 2021-07-23T07:10:13.631Z
tags: 
editor: markdown
dateCreated: 2021-07-22T07:22:03.891Z

author_profile: true
toc: true
toc_sticky: true
toc_label: "08. Sample Workshop"

categories: 
- modern-web
sidebar_main: true
---

# Web Application Security
## Single Sign-On
프로토콜
- SAML(Security Assertion Markup Lanquage)
- CAS(Cenral Authentication Service)

1. 어플리케이션이 사용자를 아이덴티티 서버(Identity Server)로 리다이렉트
2. 인증을 수행하기 위해 아이덴티티 서버에 응답
3. 인증 결과를 어플리케이션 전달

SAML
서비스 제공자(Service Provider, SP)(어플리케이션)는 SAML asserion(인증 결과)을 인증 제공자(Identity Provider, IdP)(아이덴티티 서버) 로부터 받는다.
인증 결과는 사용자 정보(이름, 이메일 주소, 인증 제공자 측에서 활용하는 사용자 ID)를 포함한다.

CAS
CAS 서버에 인증을 요청하고 성공시 어플리케이션으로 리다이렉트 되면서 서비스 티켓을 벋눈더, 어플리케이션은 받은 티켓이 유효한지 티켓을 이용해 CAS 서버로 요청을 보낸다.

## OAuth 2.0
서드 파티 서비스(third-party service) 가 사용자 데이터에 접근할 때 권한을 부여하기 위해 디자인

- 리소스 소유자 : 리소스 서버에 있는 데이터를 소유한 사람
- 리소스 서버 : 클라이언트가 접근하기를 원하는 데이터를 저장하는 서버
- 권한 부여 서버 : 클라이언트가 요청한 데이터로 접근할 수 있도록 권한 부여를 수행하는 서버
- 클라리언트 : 데이터에 접근하기를 원하는 어플리케이션

![screenshot_20210723-132857~2.png](/screenshot_20210723-132857~2.png)

## 권한 부여 방식
- 역할 기반(Role-bsed) : 리소스 종류 단위로 접근을 컨트롤
- 접근 제어 목록(Access Control List, ACL) : 특정 데이터에 대한 사용 및 접근 권한 목록, 작은 단위(fine-grainded)의 섬세한 권한 부여 가능

## 공격 예방
Open Web Application Security Project(OWASP) 에서 정의한 10가지 항목
1. 인젝션(Injection)
2. 취약한 인증(Broken Authentication)
3. 민감한 데이터 노출
4. XML 외부 개체(XML External Entities, XXE)
5. 취약한 접근 통제(Broken Access Control)
6. 잘못되 보안 구성
7. 크로스 사이트 스크립팅(Cross-Site Scripting)
8. 안전하지 않는 역직렬화(Insecure Desserialzation)
8. 알려진 약점이 있는 구성요소 활용
9. 불충분한 로깅 및 모니터링

# Spring Security

![screenshot_20210723-132914~2.png](/screenshot_20210723-132914~2.png)

1. HTTP 요청이 서버에 도달하면 그것은 스프링 시큐리티 필터 체인(Spring Security Filter Chain)을 지나간다. 
2. 스프링 시큐리티 필터 체인은 org.springframework.web.filter.DelegatingFilterProxy으로부터 요청을 전달받는다. 
2-1. 스프링 시큐리티 필터 체인은 일반적으로 springSecurityFilterChain이라는 이름을 갖는 스프링 빈으로 생성된다. 
2-2. springSecurityFilterChain은 스프링 시큐리티에 의해 생성되는 필터 빈으로 이뤄진 리스트를 포함한다. 
5. 필터를 통과하면서 스프링은 일련의 작업을 수행하며 요청이 어떻게 처리돼야 하는지 결정한다. 
6. 모든 필터를 통과하고 나면 요청은 요청 매핑을 통해 등록된 컨트롤러에 도달한다. 
7. 대부분 컨트롤러는 명령어를 실행하거나 몇 가지 정보를 가져오려고 서비스의 API를 호출 할 것이다. 
8. 제어의 흐름이 서비스로 가기 전에 스프링 시큐리티는 AOP를 통해서 메소드 단위로 권한 부여를 수행할 수  있다. 

## Spring Security 개념
1. Authentication
전송된 인증 요청 또는 인증된 주체(authenticated principal)를 위한 토큰을 표시.
인증 요청은 일반적으로 프런트엔드가 서버로 보내는 로그인 요청.
인증 요청에서 Authentication 객체는 일반적으로 인증에 사용될 사용자 명이나 비밀번호를 포함.
주체(principal)라는 용어는 Authentication이 상속하는 java.security.Principal 인터페이스.

2. GrarvtedAuthority
사용자에게 부여된 권한.
Authentication의 getAuthorities() 메소드로 사용자 에게 부여된 권한 확인 가능.

3. SecurityContext
로그인된 사용자의 정보인 Authentication는 SecurityContext 인스턴스에 캡술화돼서 HTTP 세션에 저장.
각 요청에 대해 SecurityContext를 관리할 책임이 있는 SecurityContextPersistenceFilter를 가진다.

4. SecurityContextHolder
각 요청을 처리할 때 SecurityContextHolder의 SecurityContextHolder.getContext() 메소드를 통해서 SecurityContext를 가져올 수 있다.
내부적으로 SecurityContextHolder는 SecurityContext를 ThreadLocal 변수에 보관. 
따라서 SecurityContext는 요청을 처리하는 현재 스레드에 제한. 
권한 부여를 수행할 때 스프링 시큐리티는 SecurityContext를 SecurityContextHolder에서 가져온다.