---
title: '04 - 데이터모델링'
description: 
published: true
date: 2021-05-17T04:52:10.216Z
tags: 
editor: markdown
dateCreated: 2021-04-13T01:58:20.134Z
categories:
- data-modeling
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "04. 데이터모델링"
---

## 데이터 모델링 단계
- 개념적 모델링
애플리케이션의 요구사항을 검토해 데이터 요구 사항을 발견하는 단걔
ER(Entity Relationship) 다이어그램으로 데이터 요구사항을 문서화한다.
- 논리적 모델링
데이터 요구사항을 추가로 분석하고, 범위를 정한다.
	* 데이터 정규화
  데이터의 삽입, 수정, 삭제를 수행하는 동안 데이터의 중복과 예외를 제거
  데이터의 의존성을 확인
  * 데이터 비정규화
  데이터의 성능 계선을 위한 작업
- 물리적 모델링
실제 RDBMS 나 NoSQL 와 같은 데이터베이스 설계
인덱싱과 같은 성능 향상 작업 수행

## 데이터 모델링 용어
- 엔티티(Entities)
물리적또는 논리적으로 존재하며 고유하게 식별
도메인의 복잡성을 추상화
- 속성(Attributes)
엔티티의 속성을 표현
- 관계(Relationships)
두 엔티티 간의 관계를 표현
- 기본키(Primary Key)
엔티티를 고유하게 식별하는데 활용할 수 있는 단일 속성 또는 여러 속성의 조합
	- 후보키(Candidate Keys)
  기본키는 아니지만 엔티티를 식별하는데 사용되는 키
	- 대체키(Surrogate Keys)
  시스템에서 생성된 고유의 값
  DBMS에서 자동으로 생성되는 ID 값 또는 UUID를 생성하여 사용한다.
- 외래키(Foreign Keys)
다른 엔티티 타입 간의 관계를 형성하는 방법

## 데이터 모델링 목적
- 완전성(Completeness)
현재 이용할 수 있는 요구사항에 명시된 기능의 구현을 지원
- 중복 최소화(Minimal Redundancy)
중복 데이터를 없애는데 필요한 추가적인 노력과 데이터의 잠재적인 불일치를 막기 위해 설계
- 확장성(Extensibility)
기존 데이터 구조에 최소한의 영향으로 새로운 요구사항을 수용할 수 있도록 확장
- 일관성(Consistency)
데이터 모델의 엔티티와 속성의 명명 규약의 알관성

## ER Diagram



