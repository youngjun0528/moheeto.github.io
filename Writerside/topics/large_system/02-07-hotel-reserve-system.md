# 02.07. 호텔 예약 시스템

## 관계형 데이터 베이스 선정 이유

### Read Event 처리가 Write 처리 보다 효율성이 높다

- 호텔 예약 시스템 / 웹 사이트의 방문자는 예약을 하고자 하는 사람보다 조회를 하고자 하는 사람이 훨씬 많다.
- NoSQL 데이터베이스는 Write 연산보다 Read 연산에 최적화 되어 있다.

> # 반론
> - 저자는 NoSQL는 대체로 Wrtie 연산에 최적화 되어 있다고 되어 있는데, 대부분의 사이트에서는 이에 상반되는 의견이 존재한다.
> - 아래 논문을 살펴보면 MySQL 과 MongoDB 사이의 성능 측정 결과가 있다.
> - [SQL vs NoSQL: A Performance Comparison](https://www.cs.rochester.edu/courses/261/fall2017/termpaper/submissions/06/Paper.pdf)
> - [(번역) SQL vs NoSQL: A Performance Comparison](https://velog.io/@park2348190/논문번역-SQL-vs-NoSQL-A-Performance-Comparison)
> - [A Performance Comparison of SQL and NoSQL Databases](https://www.diva-portal.org/smash/get/diva2:957015/FULLTEXT01.pdfA)
>
> - 위의 결과를 보면, 설계 방법 또는 데이터 규모, 설계 방법에 따라서 연산 속도가 결정될 뿐,
    Read/Write 연산이 데이터베이스 종류를 결정하는 중요한 조건이 아니다.
    {style="note"}

### ACID 속성 (원자성, 일관성, 격리성, 영속성)

- 이중 청구, 이중 예약과 같은 동시성 문제를 처리하기 쉬워진다.

### 모델링의 편의성

- 각 Entity 의 구조가 명확하여 시스템 설계와 개발이 편리해 진다.

## 호텔 예약 관련 대용량 데이터 베이스 설계 고려 사항
1. 현재 및 향후 예약 데이터만 저장
    - 이력성 데이터는 Archiving 또는 Cold Storage 로 별도 저장소에 저장할 수 있다. 
2. 데이터베이스 샤딩(Sharding)
    - 데이터의 특정 샤딩 키로 분할하여 저장하도록 한다.
    - 호텔 예약의 경우 객실타입 또는 지점 별로 분할이 가능하다.

### 호텔 예약 동시성 문제
#### Client 측 대응
Client 요청 전송 이후 예약 버튼 비활성화 
> Client 의 JavaScript 비활성화 등 우회 가능성이 있으므로 추전하지 않는다. 
{style="warning"}

#### 멱등(Idempotent) API
1. 시나리오 1 : 한 사람이 예약 버튼을 여러번 누른다.
   1) 사용자는 예약 주문서를 생성한다.
   2) 서버는 사용자가 요청한 멱등 키를 포함한 예약 주문서를 반환한다. : 여기서 멱등 키는 예약 Table 의 Primary Key 일 수도 있다. 
   3) 사용자는 생성된 예약 주문서를 검토하고 예약을 결정한다.
   4) 서버는 예약 주문서를 저장한다.
   5) 여러번 버튼을 누르더라도 동일한 멱등 키가 존재하기 때문에 동일한 주문서로 중복 저장은 불가능하다.
   