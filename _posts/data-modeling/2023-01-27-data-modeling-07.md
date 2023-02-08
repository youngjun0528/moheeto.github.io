---
title: "07 - 데이터 이상 현상"
description: 
published: true
date: 2021-08-20T05:00:17.841Z
tags: 
- database
- modeling
editor: markdown
dateCreated: 2021-08-16T22:16:08.537Z
categories:
- modeling
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "07 - 데이터 이상 현상"
---

## Anomaly(데이터 이상 현상)
- 데이터 모델링에서 제일 중요한 것은 무결성을 보장
- 데이터 이상현상
- 중복 때문에 Anomaly가 발생(의도하지 않은 이상 현상 발생 가능)
- Update, Delete, Insert 에서 발생할 수 있음.

### Example
![data-modeling-07-01.jpg](..%2F..%2Fassets%2Fimg%2Fdata_modeling%2Fdata-modeling-07-01.jpg)

#### Update Anomaly
홍길동을 2루수로 바꿀 때 일부 홍길동이 바뀌지 않는 현상이 있을 수 있다.
이유는 홍길동이 중복으로 나타나기 때문이다.
홍길동을 찾는 조건에서 팀번호가 관여했다면 다른 팀에 소속된 홍길동은 값이 바뀌지 않을 수 있다.

#### Delete Anomaly
김길동을 삭제할 때 의도하지 않은 타이거스, 엘리펀츠 팀 정보가 사라질 수 있다.
팀 정보가 유일했기 때문에 발생했다.

#### Insert Anomaly
장길동이라는 새로운 선수를 동록하려 할 때 팀이 결정되지 않았다면 입력할 수 없는 문제가 발생한다.
선수는 존재하는데 입력할 수 없는 이상 현상이 발생한 것이다.
다른 데이터가 존재하지 않아 원하는 데이터를 입력할 수 없는 것이 삽입 이상 현상이다.


## 정규화
### 1. 데이터 정리
![data-modeling-07-02.jpg](..%2F..%2Fassets%2Fimg%2Fdata_modeling%2Fdata-modeling-07-02.jpg)

### 2. 제1 정규화
데이터 중복을 제거하기 위한 테이블 분할
고유 식별자가 결정됨. 이를 Primary Key(PK) 라 부름.
아래 예제에서는 주문번호로 관계가 생김. (1:M)
![data-modeling-07-03.jpg](..%2F..%2Fassets%2Fimg%2Fdata_modeling%2Fdata-modeling-07-03.jpg)

### 3. 제2 정규화
2개 이상으로 구성된 PK에서 발생함.
식별자 일부에 종속된 속성(Attribute)는 제거해야 한다.
아래 예제에서는 상품 코드로 관계가 형성됨.
![data-modeling-07-04.jpg](..%2F..%2Fassets%2Fimg%2Fdata_modeling%2Fdata-modeling-07-04.jpg)

### 4. 제3 정규화
식별자 이외의 속성간에 종속관계가 존재하면 안된다.
종속관계가 있으면 중복값이 생성된다.
이행 종속관계 분해
아래 예제에서는 고객 번호로 관계가 생김.
![data-modeling-07-05.jpg](..%2F..%2Fassets%2Fimg%2Fdata_modeling%2Fdata-modeling-07-05.jpg)

### 5. 정리
![data-modeling-07-06.jpg](..%2F..%2Fassets%2Fimg%2Fdata_modeling%2Fdata-modeling-07-06.jpg)

***
> __참고자료__
>
> [RDMS Modeling 기초](https://www.inflearn.com/course/%EA%B4%80%EA%B3%84%ED%98%95%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-rdbms/dashboard)