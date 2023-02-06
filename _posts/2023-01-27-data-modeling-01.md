---
title: "01 - 관계형 데이터베이스 소개"
description: 
published: true
date: 2023-01-19T03:02:14.778Z
tags: 
- database
- modeling
editor: markdown
dateCreated: 2021-05-31T08:58:04.636Z
categories:
- modeling
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "01 - 관계형 데이터베이스 소개"
---

### 관계형 데이터베이스

1. 데이터베이스
    구조화된 데이터들의 잡합
2. 관계형 데이터베이스
    데이터들을 2차원 배열과 같은 테비을에 저장하고 관리
3. 관계 정의
    - 1:M 관계 : 부자지간 관계
    - N:M 관계 : 비즈니스 관계
    - 1:1 관계 : 부부관계, 동등관계

### 관계형 데이터베이스 특징

1. 고유 식별자
    테이블은 각 행(Record)를 식별하기 위해 고유 식별자를 정의
    Not NULL, Unique 속성
    하나 또는 여러개의 컬럼으로 정의

2. 참조 무결성
    테이블에서 외래키를 선언
    외래키는 다른 테이블에 정의된 고유 식별자를 참조
    외래 키의 값은 다른 테이블에 정의된 고유 식별자의 값의 범위를 넘을 수 없도록 제한됨.

![data-modeling-01-01.jpg](/assets/img/data_modeling/data-modeling-01-01.jpg)

### 주식별자

하나의 레코드를 고유하게 구분할 수 있는 것
하나의 컬럼 또는 여러 개의 컬럼이 모여 Primary Key를 구성
Not NULL & Unique
Primary Key는 등록시 명시 필요

#### 학생 수강신청 예제

1. 학생수강신청 관리
2. 각 학생들은 수학, 영어, 국어, 과학, 국사 최소한 3과목을 수강
3. 학생 3명의 예제를 만들고 이들이 수강신청한 과목들을 관리

![data-modeling-01-02.jpg](/assets/img/data_modeling/data-modeling-01-02.jpg)

고려사항

- 테이블 변경
    한가지 과목을 담당하는 선생님이 2명 이상으로 늘어날 경우
- 데이터 오류
    과목명을 오기
    학생명을 오기
    한 학생이 동일한 과목을 2번이상 수강신청한 경우
    한과목도 신청하지 않은 학생
    동명이인이 존재하는 경우
    한 과목이 폐강되었을 경우

***
> __참고자료__
>
> [RDMS Modeling 기초](https://www.inflearn.com/course/%EA%B4%80%EA%B3%84%ED%98%95%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-rdbms/dashboard)
