# Docker Build Error (cannot allocate memory)

## 배경

- 서버 내에서 Docker Build 또는 Run 시도시 다음과 같은 에러가 간헐적으로 발생함.
  ```Shell
  docker: Error response from daemon: failed to create endpoint recursing_aryabhata on network bridge: failed to add
  the host (veth6ad97f8) <=> sandbox (veth23b66ce) pair interfaces: cannot allocate memory.
  ```
- 유사 사례
    - https://github.com/docker/for-linux/issues/1443
    - 유사한 사례가 Open 되어 있으나, 명확한 해결책이 존재하지 않음.
    - 커널 문제 혹은 Docker Memory 관리 문제라고 추측

## 요약

> - Docker 엔진에서의 메모리와 CPU의 사용량을 Host 컴퓨터(cgroup)가 허락하는 한 모든 자원을 가져다 쓸 수 있다.
> - Docker 빌드 스크립트에서 레이어를 나누면 나눌수록 캐시된 빌드 이미지가 많아진다.
> - 캐시된 빌드 이미지가 많아질수록 불필요한 리소스 사용량이 높아진다고 추정

> 해결 방법
> - 안쓰는 빌드 캐시는 적극적으로 지워주자
> - 도커 빌드 스크립트는 최대한 최적화 해주자
> - 도커 빌드 혹은 실행시 메모리 옵션을 주자

## 원인 분석

### 1. 항상 발생하는 가?

- Docker Build 시 위의 에러 메세지는 항상 발생한다.
- 특히 <u>COPY</u> 명령어 이후 <u>RUN pip install</u> 명령어 수행시 반드시 발생한다.
- 그럼 Docker File 의 스크립트 순서를 변경하면 어떻게 되는가?
    - 에러 발생 위치만 달라졌을 뿐 어느 순간 에러는 발생한다.

### 2. 호스트 시스템의 메모리 가용량은 충분한가?

- 현재 Host 서버 에서 최대 사용 가능 메모리는 8G
- [추측] Docker Build 시 평균적으로 사용하는 메모리의 측정치는 확인하지 않았지만 분명 8G 를 다 쓰진 않을 거라 판단.
    - Case 1. 사용 가능한 호스트 메모리가 부족하다면 Docker Run 명령어 실행시 발생한다.
        - Free Memory 100m 이하 -> Docker Build (Fail)
    - Case 2. 사용 가능한 호스트 메모리가 충분하여도 1회 빌드 이후 재빌드시 발생한다.
        - Docker Build -> Another Docker Build (Fail)
    - Case 3. Docker 이미지를 1회 이상 사용한 경우 컨테이너를 삭제하여도 이미지에 종속된 빌드 캐시가 존재하는 경우 발생한다.
        - Docker Build -> Docker Run -> Container Delete -> Docker Run (Fail)

### 3. Docker Version 혹은 Linux Version의 문제인가?

- AS-IS : Docker Version [17.04], Linux OS : Ubuntu 16.04
- TO-BE : Docker Version [23.01], Linux OS : Ubuntu 22.04
- <u>docker info</u> 명령어로 Docker 설정 값을 비교하면 한가지 경우를 제외하고 다른 경우는 없음.
  - 로컬에서 재현 테스트를 진행하여도 재현되지 않음.
  - [AS-IS] 서버 설정 메세지

  ```Shell
  WARNING: No swap limit support
  ```
- 또한, Docker Version 이 올라갈수록 캐시된 빌드 레이어를 적극적으로 사용하기 시작한다.
    - Docker 컨테이너 빌드시 캐시된 레이어를 남겨 두면 빌드 속도 향상

### 분석

- Docker 는 기본적으로 Host 메모리와 CPU를 전체 사용하도록 기본설정
- No Swap Limit Support Warning 메세지
    - AS-IS
        - 이전 버전의 서버에서는 Linux 의 cgroup 에서 메모리 제한을 하지 않는다는 의미이다.
        - 즉, Docker 에서 Host의 Swap Memory를 제한 없이 사용 가능
    - To-BE
        - 그럼, Ubuntu 20.04 이후 부터는 Host Lixux의 cgroup 에서 서버내 프로세스에 대한 메모리와 스왑메모리를 기본 관리하기 시작
        - 즉, Docker 에서 Host의 Swap Memory 제약은 Host 에서 제어
    - 대략 참고 내용
        - https://blog.2dal.com/2017/03/27/docker-and-oom-killer/
- 만약, Docker Build 또는 Run 시 메모리 제한 옵션을 주면 각 컨테이너가 쓰는 리소스가 지정되어 자원 할당량에 우선순위가 변동되지 않을까?
- 만약, 도커 빌드 스크립트를 최적화 해서 빌드 레이어를 최대한 줄이고 빌드 캐시를 삭제하면 좋지 않을까?
- 만약, 불필요한 빌드 캐시를 삭제하면 Docker 에서 사용 가능한 메모리 영역이 확보되지 않을까?

### 조치 및 테스트

> - 최종적으로 불필요한 빌드 캐시는 반드시 지워야 하며,
> - 서버 환경의 안정성을 위해 메모리 제한 옵션 및 스크립트 최적화를 고려해야 한다.

#### Case 1. Docker Build 또는 Run 시 메모리 제한 옵션

- https://docs.docker.com/config/containers/resource_constraints/
    ```Shell
    docker run -it -m 2500m ubuntu /bin/bash
     ```
    - swap 메모리를 지정하지 않는 경우 default 값으로 지정된 메모리의 2배가 사용된다.

#### Case 2. 도커 빌드 스크립트를 최적화

- Docker Build 스크립트 최적화
    - https://docs.docker.com/engine/reference/builder/

1. 애플리케이션 언어에 적합한 경량의 베이스 이미지를 사용하라.
2. 필수 패키지 및 .dockerignore 를 사용하여 필요한 패키지 및 소스만 포함하라
    - 아래는 전체 파일을 제외하고 '!' 로 표시된 폴더만 포함한다.
   ```Docker
    *
   !requirement.txt
   !template/*
   !test/*
   !src/*
     ```
3. COPY 그리고 RUN 등의 레이어를 만드는 명령어를 최소화하라.
    - AS-IS
   ```Docker
   RUN apt-get update
   RUN pip install boto3
   RUN pip install requests
   COPY ./test /home/test
   COPY ./src /home/src
    ```
    - TO-BE
    ```Docker
    RUN apt-get update && pip install boto3 && pip install requests
    COPY ./test/* ./src/* /home/ 
    ```
4. 멀티 스테이지로 빌드와 런타임 환경의 이미지를 분리하라.
    - base.dockerfile : 환경 변수 설정 및 패키지 설치 등 변동 사항이 거의 없는 부분
    - target.dockerfile : 실제 소스가 들어가는 부분

#### Case 3. 불필요한 빌드 캐시를 삭제

- https://docs.docker.com/engine/reference/commandline/builder_prune/
- 디스크에 남아 있는 캐시를 삭제하는 명령어로 모든 Build 및 Run 이 끝나면 사용한 컨테이너와 이미지를 삭제하고 사용한다.

## 결론

- 최총적으로 서버내 메모리와 CPU가 충분하다면 문제가 없는 상황이지만,
    - 로컬 Linux 22.04 의 32G 메모리 i7 CPU 에서는 어떤 제약에도 동일한 에러가 발생하지 않음.
- 위와 같이 특이한 Case의 경우에는 빌드 캐시를 자주 지워주는 방법 외에는 별다른 해결 방안이 없다.
- 다른 시스템과 연관이 없고, 테스트 서버라면 Linux 커널 업데이트 및 Docker 재설치를 통해서 재현이 되는지 확인하겠지만,
- 물리적인 환경 제약으로 인해서 불가능하다. ( 다른 서비스가 서버 내에서 돌고 있음 ..ㅠㅠ..)