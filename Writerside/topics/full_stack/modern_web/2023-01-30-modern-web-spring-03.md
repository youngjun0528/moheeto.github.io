# 07. RESTful API 설계

## RESTful API 특징

### 아키텍처의 제약사항

- 클라이언트-서버(Client-Server)  
  사용자 인터페이스는 데이터 저장소와 분리된다.  
  Web Application의 Front-End는 브라우저 내부의 Client로 존재하며, Client는 API를 통해 서버와 통신한다.  
  Evnet-Driven Integration Architecture는 네트워크를 통해 이벤트를 송수신한다.

- 무상태성(Stateless)  
  Client에서 Server 로의 각 요청은 해당 요청을 이해하는데 필요한 모든 정보를 포함한다.

- 캐시(Cache)  
  시스템에서 요청에 대한 응답에 암묵적 혹은 명시적 캐시 가능 또는 불가능을 알려주어야 한다,  
  HTTP Header를 통해 Cache를 제어할 수 있다.

- 유니폼 인터페이스  
  시스템 아키텍쳐가 단순해지고 상호작용의 가시성이 향상된다.  
  리소스 식별, 표현을 통한 리소스 조작, 자기 서술 메세지, Application 상태 엔진으로서의 하이퍼미디어

- 계층형 시스템  
  구성요소의 동작을 제한하는 계층으로 구성되어 각 계층은 독립적이다.

- \[ 선택사항 \] 주문형 코드(Code -On-Demand)  
  REST는 Applet 또는 Script 형태로 코드를 다운로드 하고 실행하여 Client 기능을 확장한다.  
  HTTP가 RESTful API의 필수 요소는 아니지만, 대부분의 API는 HTTP를 통해 구성된다.

### 인터페이스 제약사항

- 리소스 식별  
  서비스에서 리소스를 Client에게 제공할 수 있게 하는 정보를 추상화.  
  리소스는 URI를 통해 고유하게 식별 될 수 있다.

  ```
  https://api.example.com/v1/tasks/1
  ```

- 표현을 통한 리소스 조작  
  리소스는 정보의 추상화로 XML, HTML, JSON으로 표현 형식을 가진다.  
  HTTP 메소드(GET, POST, PATCH, PUT, DELETE) 를 통해 리소스를 조작한다.

- 자기 서술 메세지  
  수신자가 이해하는 데 필요한 모든 정보를 포함하는 메세지

- Application Status Engine으로서의 Hypermedia (HATEOAS)  
  [https://en.wikipedia.org/wiki/HATEOAS](https://en.wikipedia.org/wiki/HATEOAS)

- 상세 내용 작성 To-Do

### 특징

리소스 중심적  
API는 리소스를 중심으로 구성, 리소스는 서비스가 리소스를 클라이언트에게 제공할 수 있게 추상화  
리소스 내부 구조는 API를 통해 은닉화되어 있어야 한다.

URI를 통한 식별 가능  
명사 시간으로 식별

```
# 다수의 사용자 정보 GET
https://api.example.com/v1/users

# ID가 537 인 특정 사용자 정보 GET
https://api.example.com/v1/users/537
```

- 버전관리  
  API의 변경 사항을 관리하는 방식  
  URL에 Version 명을 포함한다.  
  Front-End & Back-End 방식의 현대 Web Application 에서는 서로 간의 상호작용하는 방식이 변경될 수 있기 때문에 전체 마이그레이션 시간이 없다면 버전을 통해서 API를 관리하는 것도
  한가지
  방법이다.

- 무상태성  
  상태가 없다면 Client와 Server 간의 관계가 유지될 필요가 없기 때문에 서비스의 확장성을 매우 높아진다.  
  모든 서버가 API Client의 요청을 처리할 수 있다.  
  쉽게 이야기하면, Server와 Client 사이의 종속성을 없앤다.

- 페이징 처리  
  일반적으로 리소스의 Collection을 요청할 때 단일 응답으로 모든 리소스를 검색하여 전달하지 않는다.

- Offset 방식
- Curser 기반

- 검색과 정렬  
  매개변수 설정을 통해 검색을 수행한다. 굳이 search 리소스를 추가하지는 않는다.
  ```
  https://api.example.com/v1/orders?status=completed
  https://api.example.com/v1/orders/search?status=completed // NOT USE!!!
  ```
  정렬 또한 sort라는 매개변수를 통해서 진행한다.
  ```
  https://api.example.com/v1/orders?status=completed&sort=-compleredDate
  ```

- 보안  
  모든 API는 SSL을 사용해야 한다.
    - API Client가 Random으로 생성된 Access Token을 HTTP 기본 인증값으로 포함한다.

    - OAuth2를 지원해 3-party App. 을 통해 Access 권한을 획득한다.

    - JWT(Json Web Tokens) 을 사용하여 개방형 표준을 사용한다.  
      로그 상에서 URL 캡쳐가 가능하므로, 개인 비밀번호는 URL에 관련 정보를 추가하지 않는다.  
      API 매개변수의 입력 값을 항상 검증하고 유효성 검사를 수행한다.

#### HTTP Method를 통한 작업 정의

- GET  
  요청된 리소스의 세부사항을 응답한다.  
  GET 작업시 리소스의 상태에 영향을 끼쳐서는 안되며, 여러번 호출가능하며, 응답을 캐싱할 수 있다.

- POST  
  새로운 리소스를 생성한다.  
  리소스 식별자는 Server에 의해 생성되며, 새로운 리소스에 대한 세부사항이 포함된다.

- PUT  
  새로운 리소스를 생성하거나 기존 리소스를 업데이트한다.  
  PUT 요청의 리소스 식별자는 Client에 의해 지정되고, POST 요청은 Server에 의해 생성된다.  
  만약 Client에서 리소스 식별자 생성을 제한한다면, 업데이트 용도로만 사용되며 업데이트 대상이 없을 경우 HTTP 404 응답이 반환한다.  
  PUT요청을 여러번 반복하더라도 멱등성을 가지기에 상태는 한번만 변화할 뿐 추가적인 상태변화는 없다.

- PATCH  
  기존 리소스의 일부를 업데이트한다.  
  PUT 요청에서의 업데이트는 전체 리소스에 대한 업데이트이므로 전체 본문이 필요하나,  
  PATCH 요청에서의 업데이트는 표현의 일부분만 필요하다.  
  PATCH 요청을 여러번 반복한다면 멱등성을 안가지고 있기에 계속 리소스의 상태는 변화한다.

- DELETE  
  리소스를 제거한다.  
  DELETE를 여러번 수행하면 리소스의 상태는 항상 같다.

#### HTTP 상태코드

| Code | Status                | Description                                                                  |  
|------|-----------------------|------------------------------------------------------------------------------|  
| 200  | OK                    | GET, PUT, PATCH 요청이 성공했음                                                     |  
| 201  | Created               | 새로운 리소스를 생성하기 위한 POST, PUT 요청이 성공                                            |  
| 204  | No Content            | DELETE 요청 성공                                                                 |  
| 304  | Not Modified          | 마지막 요청 이후 리소스가 수정되지 않음                                                       |  
| 400  | Bad Request           | Client Error로 Server가 해당 요청을 처리할 수 없거나 못한 경우, 요청이 잘못된 형식이거나, 유효성 검사에서 실패한 경우 |  
| 401  | Unauthorized          | 인증자격을 포함하지 않거나, 인증에 실패한 요청                                                   |  
| 403  | Forbidden             | 인증된 사용자에게 허용되지 않는 리소스에 접근한 경우                                                |  
| 404  | Not Found             | 존재하지 않는 리소스에 대한 요청                                                           |  
| 409  | Conflict              | 리소스 상태가 충동했기 때문에 리소스 상태를 변경하려는 시도가 서버에서 처리 불가능                               |  
| 410  | Gone                  | 요청된 리소스가 더이상 이용할 수 없음                                                        |  
| 429  | Too Many Request      | Client가 주어진 시간동안 너무 많은 요청을 보냈고 거부되었음                                         |  
| 500  | Internal Server Error | 서버가 예기치 않은 조건을 발견해 해당 요청을 처리하지 못함                                            |
