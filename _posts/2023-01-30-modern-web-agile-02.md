---
title: '02 - 코드설계'
description: 
published: true
date: 2021-05-24T03:17:42.888Z
tags: 
editor: markdown
dateCreated: 2021-05-17T05:04:17.830Z

author_profile: true
toc: true
toc_sticky: true
toc_label: "08. Sample Workshop"

categories: 
- modern-web
sidebar_main: true
---

# 애자일 코드 설계
## 코드설계의 단계
### 아키텍쳐 단계
시스템 걸계 방식, 서비스 구성도, 서브 시스템 간의 통신 방법 정의

### 추상화 단계
서브 시스템의 패키지와 주요 컨포넌트. 핵심 클래스와 클래스와의 관계 정의

### 구현단계
클래스, 필드와 메소드 구현.

#### 테스트 주도 개발(Test-driven development)
작성한 코드가 올바르게 실행 되는지 확인하기 위한 단위 테스트 생성
단위 테스트를 소스 코드의 클라이언트로 사용해 코드 설계를 개선하고 분리할수 있으며, 코드 품질을 개선하는데 도움을 준다.

#### 인수 테스트(Acceptance test)
고객이 지정한 사용자 스토리의 세부사항을 확인하기 위해 인수 테스트를 작성.
인수 테스트는 반복적으로 실행 가능하다.

#### 리팩터링(Refactoring)
필요할 때마다 디자인 원칙을 적용하거나 디자인 패턴을 사용하기 위해 코드를 변경해 설계를 개선하는 기술

#### 현재 스토리에 집중하기
현재 스프린트의 사용자 스토리에 중점을 두고 설계 구현한다.


## 나쁜 설계
### 경직성(Rigidity)
시스템을 변경하기 어렵다. 변경하려면 시스템의 다른 부분까지 많이 변경해야 하기 때문이다.
### 취약성(Fragility)
변경하려면 시스템에서 그 부분과 개념적으로 아무런 관련이 없는 부분이 망가진다.
### 부동성(Immobility)
시스템을 다른 시스템에서 재사용할 수 있는 컴포넌트로 구분하기 어렵다.
### 점착성(Viscosity)
옳은 동작을 하는 것이 잘못도니 동작을 하는 것보다 더 어렵다.
### 불필요한 복잡성(Needless complexity)
직접적인 효용이 전혀 없는 기반구조가 설계에 포함돼 있다.
### 불필요한 반복(Needless repetition)
단일 추상 개념으로 통합할 수 있는 반복적인 구조가 설계에 포함되어 있다.
### 불투명성(Opacity)
읽고 이해하기 어렵다. 그 의도를 잘 표현하지 못한다.

# 디자인 원칙(SOLID 디자인 원칙)과 디자인 패턴
## 단일 책임 원칙(SRP)
한 클래스는 변경에 대한 이유를 하나만 가져야 한다.
한가지 메소드는 단 하나의 기능만 수행해야 한다.

## 개방-폐쇄 원칙(OCP)
소프트웨어 아티팩트(artifact)는 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다.

## 리스코프 치환 원칙(LSP)
서브 타입은 베이스 타입으로 치환 가능해야 한다.
즉, 서브타입의 메소드는 베이스 타입의 메소드를 대체할 수 있어야 한다. 베이스 타입의 관점에서 모든 서브 타입은 동일하게 동작하여야 한다.

## 인터페이스 분리 원칙(ISP)
클라이언트는 자신이 사용자히 않는 메소드에 의존하도록 강제되어서는 안된다.

## 의존 관계 역전 원칙(DIP)
상위 수준의 모듈은 하위 수준의 모듈에 의존해서는 안된다. 둘 모두 추상화에 의존해야 한다.
추상화는 구체적인 사항에 의존해서는 안된다. 구체적인 사항이 추상화에 의존해야 한다.
상위 수준의 모듈은 비즈니스 규칙 / 하위 수준의 모둘은 구현체의 세부사항

# 아키텍쳐
## 계층형 아키텍쳐
복잡한 시스템을 별도의 계층으로 나누는데 활용하는 일반적인 기술
일반적으로 프레젠테이션 계층 / 비즈니스(어플리케이션/서비스/도메인) 계층 / 데이터 접근 계층

## 헥사고날 아키텍쳐
도메인 주도 설계(DDD). 도메인 모델을 포함한 애플리케이션을 중심으로 데이터베이스, 검색엔진, 메세지 큐등 외부 인프라를 통한 이벤트를 수신하는 어댑터를 통해 서비스를 구성 확장한다.
