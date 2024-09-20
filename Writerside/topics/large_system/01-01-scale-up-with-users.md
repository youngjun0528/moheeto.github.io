# 01.01. 사용자 수에 따른 규모 확장성

## 단일 서버

![](01_01_Image_01_01.png)
> 1. Client 는 Domain Name 이 어떤 IP 주소를 가지고 있는지 DNS 서버에 질의한다.
> 2. Web Server 의 IP 주소를 가지고 HTTP Request 를 전달한다.
> - Web Server 는 HTTP Response 를 반환한다.

## Web 계층과 데이터 계층 분리

![](01_01_Image_01_02.png)
> 1. 단일서버 구조에서 웹서버는 Client 의 Traffic 만 처리
> 2. 데이터 CRUD 는 Database 서버에서 처리

### 데이터베이스 서버

- 데이터 간의 관계성, 대용량 처리가 필요할 때 - 관계형 데이터베이스 (RDBMS)
- 빠른 응답시간, 비정형데이터 처리가 필요할 대 - 비-관계형 데이터베이스 (NoSQL)

## 부하 분산과 다중화

### Scale-Up vs. Scale-Out

1. Scale-Up
    - 수직적 규모 확장
    - 한대의 서버에 무한정 Resource 자원을 늘릴 수는 없다.
2. Scale-Out
    - 수평적 규모 확장
    - 로드밸런서 (Load Balancer) 구성
        - 장애 발생시 자동 복구 (Fail-Over) / 다중화(Redundancy) 제공 가능.

### 로드밸런서와 데이터베이스 다중화

![01_Image_01_03.png](01_01_Image_01_03.png)
> 1. Traffic 이 과도하게 들어올 경우 Load Balancing 을 통해서 부하 분산 처리
> 2. Web Server 의 가용량이 부족한 경우 Load Balancer Set 에 서버 추가 투입
>   - Web Server 1 이 장애 발생으로 Down 이 되어도 나머지 Web Server 를 통해서 Traffic 처리
> 3. 일반적으로 조회 작업이 데이터 조작 작업보다 많으므로, Master DB는 Insert/Update/Delete 연산 수행, Sub(Slave) DB는 Read 연산 수행
> 4. Sub(Slave) DB 장애 발생시 다른 Slave DB 또는 Master DB 가 Read 연산 대체
>   - Master DB 장애 발생시 다른 Slave DB 가 Master DB 로 전환하고, 데이터 보정 및 동기화를 위한 복구 스크립트 수행 여부를 결정함.
>   - Master DB를 다중 마스터(Multi-master) 또는 원형 다중화(Circular-replication) 검토 필요

> # Multi Master : Master DB를 복제하여 운영
> ![Multi-master](https://www.oreilly.com/api/v2/epubs/9781789539974/files/assets/91c3514d-b688-4f9f-bf46-939bcaa96781.png)

> # Circular Replication : 각 RDS 의 변경 사항은 순환 참조
> ![Circular-replication](https://www.jamescoyle.net/wp-content/uploads/2014/07/mysql-circular-replication.png)

## Client Data Storage

### 캐시

- 읽기 주도형 캐시 전략(Read-Through Caching Strategy)
    - 값비싼 연산 결과 또는 자주 참조되는 데이터를 메모리 안에 두고, 뒤이은 요청이 보다 빨리 처리될 수 있도록 하는 저장소
    - 요청을 받은 웹 서버는 캐시에 응답이 저장되어 있는지 확인하고, 만약 저장되어 있다면 해당 데이터를 클라이언트에 반환. 만약 저장되어 있지 않다면 데이터베이스에서 데이터를 찾아 캐시에 저장한 뒤
      클라이언트에 반환

#### 고려사항

- Q1. 캐시는 어떤 상황에 바람직한가?
    - A1. 데이터 갱신은 자주 일어나지 않지만, 참조는 빈번하게 발생한다면 고려
- Q2. 어떤 데이터를 캐시에 두어야 하는가?
    - A2. 비활성 데이터를 저장한다. 캐시에 저장된 데이터는 캐시 서버가 재시작 되면 모든 데이터는 사라질 수 있다는 고려사항이 필요하다.
- Q3. 캐시에 보관된 데이터는 어떻게 만료되는가?
    - A3. TTL(Time to Live) 기능을 황용하여 만료 정잭을 구성한다. 클라이언트가 얼마나 자주 데이터를 Read 하는지에 따라 만료 정책은 달라진다.
- Q4. 일관성은 어떻게 유지되는가?
    - A4. 데이터 저장소릐 원본과 캐시내 사본의 일관성을 위해 원본 갱신 트랜잭션과 캐시 사본 갱신 트랜잭션은 단일 트랜잭션으로 처리되어야 한다.
- Q5. 장애는 어떻게 대응해야 하는가?
    - A5. 캐시 서버는 분산 처리되어야 한다.
    - > # 단일 장애 지점 (Single Point of Failure, SPOF)
      > 어떤 특정 지점에서의 장애가 전체 시스템의 동작을 중단시켜버릴 수 있는 경우
- Q6. 캐시 메모리는 얼마나 크게 잡을 것인가?
    - A6. 캐시 메모리가 너무 작으면 액세스 패턴에 따라 데이터가 너무 자주 캐시에서 밀려난다. (Eviction) 이를 대비하기 위해 캐시메모리는 과할당 (OverProvisioning) 하여 장애를
      대비한다.
- Q7. 데이터 방출 (Eviction) 정책은 무엇인가?
    - A7. 캐시가 가득차버리면 추가로 캐시에 데이터를 넣어야 할 경우, 기존 데이터를 내보내는것을 데이터 방출 정책이라 한다.
    - > # 데이터 방출 정책 (Eviction)
      > ## LRU ( Least Recently Used )
      > 마지막으로 사용된 시점이 가장 오래된 데이터를 내보내는 정책
      > ## LFU ( Least Frequently Used )
      > 사용빈도가 가장 낮은 데이터를 내보내는 정책
      > ## FIFO ( First In First Out )
      > 가장 먼저 캐시에 들어온 데이터를 가장 먼저 내보내는 정책

> # 캐시 서버 종류
> ## 로컬 캐시
> 서버 리소스 ( Disk , Memory 등 ) 에 데이터를 임시 저장하는 기술이다. 단일 서버로 구성되어 있는 경우, 혹은 로컬 PC 에서만 작동하는 프로그램의 경우 사용될 수 있다.
> ## 웹 캐시
> REST 나 HTTP Static Resource 요청과 같이 중간에 Apache 나 Nginx 와 같이 웹 서버에서 캐싱 설정을 통해 캐시를 제공할 수 있다.
> ## In-Memory Database
> 데이터 저장이 메모리에 저장되는 구조로 Key-Value 방식으로 데이터가 저장되며, 메모리 방식이기 때문에 빠른 응답이 필요한 캐시서버로 활용될 수 있다. 또한 Snapshot 을 통해 데이터 백업이 가능하며,
> Master-Slave 구조로 분산 배치가 가능하다.

### 콘텐츠 전송 네트워크 CDN

- 정적 콘텐츠를 전송하는데 쓰이는, 지리적으로 분산된 서버의 네트워크. 주로 이미지, 비동, CSS 등의 정적 Resource 를 캐시할 수 있다.

#### Flow

- 특정 서비스 사업자는 CDN 서비스 사업자가 제공하는 도메인을 사용 ( eg. Cloudfront 클라우드프론트, Akamai 아카마이 )
- Client 가 특정 서비스에 접속을 하고자 한다면, 우선 서비스 도메인에 위치한 CDN 서버에 먼저 사이트 정적 파일을 확인한다.
- CDN 서버에 요청한 파일이 있다면 해당 파일을 Return 하고, 없다면 Origin 서버에 확인하여 Return 한다.

#### 고려사항

- 비용
    - CDN은 보통 Third-party 사업자에 의해 운영되며, 전송량에 따라 비용이 청구된다.
- 적절한 만료 시한 설정
    - 콘텐츠의 시간 민감도에 따라 CDN 서버 내에 유지해야 할 콘텐츠 수명 기간을 정해야 한다.
- CDN 장애에 대한 대처 방안
    - 대부분의 CDN 서비스는 이중화가 되어 있고 전체 CDN 서비스가 내려갈 일은 드물 것이다.
    - CDN 을 사용할 수 없는 장애 상황에 대비하여 우회 경로를 준비해야 한다.
    - > # 우회 방안
      > CDN의 도메인을 Client의 최종 도메인이 아니라 중간 도메인을 사용한다.
      > 그럼 CDN 장애 상황에 따라 ㅇ연하게 대처 가능하다.
      > 문제는 도메인을 이중으로 관리를 해야 한다는 유지보수의 불리함과 도메일은 2번 거침으로써 나타나는 성능 상의 불리함이 있다.
      > Client -> 서비스 제공자 도메인 -> (First) CDN 제공자 도메인 ----> CDN 서버 or 서비스 제공자 서버
      > Client -> 서비스 제공자 도메인 -> (Second) 서비스 제공자 서버
- 콘텐츠 무효화 방법
    - CDN 서비스 API 를 사용
    - 콘텐츠 Object 에 대한 Version 관리 이요

## 무상태(Stateless) 웹 계층

- 상태 정보 (사용자 Session 데이터) 를 웹 계층에서 제거하여 RDS 나 NoSQL 에 별도 저장하고 필요할 때 가져오는 방식

### 상태 정보 의존 아키텍처

- Client 의 상태 정보를 웹 서버에 저장한다면, 해당 Web 서버와 Client 는 1:1 매핑이 되어 관리되어야 한다.
- Load Balancer 이러한 매핑관리를 위하여 고정 Session 이라는 기능을 제공하지만, 사용자 수에 따른 웹 서버의 Auto Scaling 을 어렵게 한다.

### 무상태 아키텍쳐

- Client 의 상태정보를 서버에 저장하는 것이 아닌, 공유 저장소 (Shared Storage)에 저장함으로써 사용자 수에 따른 웹 서버의 Auto Scaling 을 쉽게 한다.
- 서버에서 Client 의 상태정보를 알기 위해서는 공유 저장소 질의를 통해서 확인한다.

## 데이터 센터

- 보통 데이터 센터는 이중화를 통해서 분산 보관되어 있다.
- 트래픽 우회 : 지리적 라우팅(geoDNS-Routing)
    - 분산된 데이터 센터는 지리적 라우팅(geoDNS-Routing)을 통해 처리되는데, 접속자의 IP 주소에 따라서 가장 가까운 데이터 센터로 안내된다.
    - 만약, 하나의 데이터 센터가 장애 발생시 모든 요청은 장애가 발생하지 않은 데이터 센터로 트래픽을 우회 전송 처리된다.
- 데이터 동기화(Synchronization)
    - 분산된 데이터 센터 중 하나가 FailOver 되고 복구가 되었을대, 데이터 동기화에 대한 논의가 필요.
- 테스트와 배포
    - 모든 Region 에 동일한 서비스가 제공될 수 있도록 배포 전략을 가져야 한다.
    - 테스트 또한 각 Region 의 데이터 센터는 동일하게 구성되어 있는지 테스트가 필요하고, 각 데이터 센터와 연결된 네트워크 사업자가 있다면 해당 전용선에 대한 가용성 테스트도 필요하다.

