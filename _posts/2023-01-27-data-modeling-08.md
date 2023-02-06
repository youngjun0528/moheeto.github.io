---
title: "08 - 정규화"
description: 
published: true
date: 2021-08-20T05:00:15.403Z
tags: 
- database
- modeling
editor: markdown
dateCreated: 2021-08-20T05:00:07.874Z
categories:
- modeling
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "08 - 정규화"
---

## 제 1정규화
모든 속성은 반드시 하나의 값을 가져야 한다
값이라는 것은 원자성(ATOM)을 가져야 한다. 즉 더 이상 쪼갤 수 없는 하나의 값만을 가져야 한다

### 제1 정규화 대상
1. 다가 속성이 사용된 릴레이션
다가 속성은 같은 종류의 값을 여러 개 가지는 속성을 의미
객 번호를 알더라도 유일하게 식별할 수 있는 전화번호가 없음
따라서 전화번호는 주 식별자인 고객번호에 함수적으로 종속되지 않았다.
따라서 한 속성에는 반드시 하나의 값을 가져야 한다는 1정규형에 어긋나는 릴레이션이다.
FD: 고객번호->고객명
FD1: 고객번호-> 주민번호
FD2: 고객번호-> 전화번호 ????? <여러개 존재>
![data-modeling-08-01.jpg](/assets/img/data_modeling/data-modeling-08-01.jpg)

2. 복합 속성이 사용된 릴레이션
주소 체계와 같이 시 + 군 + 읍/면/동 형식으로 분리되어 있는 경우 별도 테이블로 분리하는 것
![data-modeling-08-02.jpg](/assets/img/data_modeling/data-modeling-08-02.jpg)

3. 유사한 속성이 반복된 릴레이션
모든 속성이 단일 값을 사용해야 한다
한 릴레이션에서 반복 형태의 속성이 있어서는 안 된다는 것
![data-modeling-08-03.jpg](/assets/img/data_modeling/data-modeling-08-03.jpg)
## 제2 정규화
두 개 이상으로 구성된 PK에서 발생
R의 모든 속성이 후보 식별자 전체에 종속
일반 속성이 후보 식별자 전체에 종속되지 않고 일부에 종속된다면 2정규형 아니다.
따라서 2정규형이기 위해서는 모든 비 식별자 속성은 후보 식별자 속성에 완전 함수 종속돼야 한다

일반 속성 중에서 후보 식별자 전체에 종속적이지 않은 속성을 찾아 기본 엔터티에서 제거하고, 그 속성의 결정자를 주 식별자로 하는 새로운 상위 엔터티를 생성
![data-modeling-08-04.jpg](/assets/img/data_modeling/data-modeling-08-04.jpg)

- 업체코드로 업체명을 식별할 수 있다.
- 상품가격과 업체코드로 납품 가격을 식별할 수 있다.
![data-modeling-08-05.jpg](/assets/img/data_modeling/data-modeling-08-05.jpg)

## 제3 정규화
식별자가 아닌 일반 속성 간에는 종속성이 존재하지 않는다
3정규형의 대상이 되는 속성을 이행 종속 속성이라고 함
일반 속성간의 종속 관계를 분해하는 것
![data-modeling-08-06.jpg](/assets/img/data_modeling/data-modeling-08-06.jpg)

## BC정규화
릴레이션에 존재하는 종속자는 후보 식별자가 아니어야 한다
만약, 아래와 같이 하나의 교수가 하나의 과목만 가르칠수 있다면, 
- 과목코드로 교수코드를 식별할 수 있다.
- 학생코드와 과목코드로 학점을 식별할 수 있다.
![data-modeling-08-07.jpg](/assets/img/data_modeling/data-modeling-08-07.jpg)

***
> __참고자료__
>
> [RDMS Modeling 기초](https://www.inflearn.com/course/%EA%B4%80%EA%B3%84%ED%98%95%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-rdbms/dashboard)