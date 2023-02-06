---
title: "09 - 역졍규화와 인덱스"
description: 
published: true
date: 2021-08-20T05:17:52.728Z
tags: 
- database
- modeling
editor: markdown
dateCreated: 2021-08-20T05:15:37.873Z
categories:
- modeling
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "09 - 역졍규화와 인덱스"
---

# 역정규화
- 효율을 위해서 정규화된 결과의 일부를 수정하여 중복을 허용
	- 정규화되지 않은 상태에서 중복을 허용하는 것과는 다름
	- 대부분 JOIN 시 발생되는 엄청난 계산량을 해결하기 위해서 사용
	- select  (select count(*) a from table), (select count(*) b from table),
![data-modeling-09-01.jpg](/assets/img/data_modeling/data-modeling-09-01.jpg)
  
# 인덱스
## B-Tree 인덱스 구조
- 인덱스 탐색은 Root->Branch->Leaf->디스크저장소 순
- 인덱스의 두번째 컬럼은 첫 번째 컬럼에 의존해서 정렬됨
- 두번째 컬럼의 정렬은 첫번째 컬럼이 똑같은 열에서만 의미가 있음
- 인덱스 키값의 크기가 크면 여러 페이지를 읽어들이게 되어 성능저하 현상 발생
- 인덱스 컬럼 선택 기준
	- 카디널리티(Cardinality)가 가장 높은 것 순으로 선택
	- 카디털리티는 select count(distinct(인덱스컬럼))으로 확인
- 조회 쿼리 사용 시 인덱스를 태우려면 최소한 첫번째 인덱스 조건은 조회조건에 포함되어야 함
- 첫번째 인덱스 컬럼이 조회 쿼리에 없으면 인덱스를 타지 않는다
- between, like, <, > 등 범위 조건은 해당 컬럼은 인덱스를 타지만 그 뒤 인덱스 컬럼들은 인덱스가 사용되지 않음
- 인덱스로 사용된 컬럼값 그대로 사용해야만 인덱스가 사용

## 실행계획
인덱스 테이블 조회 시 사용되는 용어들
### Clustered Index Seek
클러스터드 인덱스가 있는 테이블의 경우 데이터는 클러스터드 인덱스의 키로 정렬
이 키로 데이터를 찾을 경우 발생
### RID Lookup
클러스터드 인덱스가 없는 테이블의 경우 데이터는 추가된 순서대로 쌓이게 됨 (힙 테이블이라고 부름)
이때 넌클러스터드 인덱스를 만들게 되면 넌클러스터드 인덱스의 리프레벨에는 데이타의 위치를 가르키는 RID가 기록
넌클러스터드 인덱스로 데이터를 찾을 경우 RID Lookup이 발생
### Key Lookup
클러스터드 인덱스가 있는 테이블에 넌클러스터드 인덱스를 만들면 이 인덱스의 리프레벨에는 RID가 아닌 클러스터드 인덱스의 키가 기록
이 넌클러스터드 인덱스로 데이터를 찾을 경우에는 넌클러스터드 인덱스의 리프레벨에서 클러스터드 인덱스의 키를 확인한 후 이 키를 이용해서 데이터를 찾는 "Key Lookup"이 발생
### Clustered Index Scan (Table Scan)
주소록 테이블에 전화번호로 인덱스를 만들었을 때 찾고 싶은 데이터는 전화번호의 4번째가 7이고 다섯번째와 여섯번째의 차가 3인 그런 데이타라고 가정하면 우리가 전화번호부를 이용해서 이런 데이터를 찾으려고 해도... 결국 전화번호부 책을 몽땅 뒤질수밖에  없는 상황 SQL SERVER도 마찬가지
인덱스를 이용해서 저런 데이타를 찾을 수가 없기 때문에 결국 테이블을 몽땅 스캔
### Index Scan
Leaf Level의 첫번째 페이지부터 데이터 검색을 수행하는 방법
일반적으로 얘기하는 Table Scan과 동일한 방법이지만, 그 대상이 Leaf Level의 인덱스 페이지 
그러나, Clustered Index의 경우 Leaf Level이 곧 Data Page이기 때문에 Index Scan이란 용어 대 신 Clustered Scan 이라는 용어로 표현됨
### Index Seek
B-Tree 구조상의 Root 페이지부터 Leaf Level까지 검색 경로를 따라 수행되는 방법
Leaf Level을 제외한 상위 각 레벨에서 1페이지씩을 검색하게 됨
