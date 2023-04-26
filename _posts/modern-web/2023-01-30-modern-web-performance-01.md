---
title: "01 - Performance"
description: 
published: true
date: 2021-08-11T03:17:55.976Z
tags: 
- modern-web
- FrontEnd-Performance
editor: markdown
dateCreated: 2021-05-10T07:06:23.366Z
categories: 
- performance
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "01 - Performance"
---

# 최적화 항목
> - 이미지 사이즈 최적화
> - 코드 분할
> - 텍스트 압축
> - 병목 코드 최적화

# Tools
## Lighthouse(Google Chrome)
![performance-01.png](..%2F..%2Fassets%2Fimg%2Fperformance%2Fperformance-01.png)
- Performance 번수는 웹페이지의 종합 성능으로 웹 바이탈(Web Vitals) 이라고도 한다.
- First Contentful Paint(FCP) : 가중치 10%
  - Page Loading 할 때, 브라우저가 DOM 의 첫번째 부분을 랜더링 하는데 걸리는 시간
- Speed Index(SI) : 가중치 10%
  - 페이지의 콘텐츠가 시각적으로 표시되는 속도
  - 전체 Loading 속도를 의미하지 않으며, 각 콘텐츠가 로딩되는데 걸리는 평균적인 속도를 의미함.
- Largest Contentful Paint(LCP) : 가중치 25%
  - 화면 내의 가장 큰 이미지나 텍스트가 로딩되는데 걸리는 시간
- Time to Interactive(TTI) : 가중치 10%
  - 사용자가 페이지와 상호작용이 가능한 시점까지 걸리는 시간
- Total Blocking Time(TBT) : 가중치 30%
  - 페이지가 사용자 입력에 응답하지 않도록 차단된 시간
  - FCP와 TTI 사이에 Main Thread 가 점유하고 있는 기간
- Cumulative Layout Shift(CLS) : 가중치 15%
  - 페이지 로드 과정에서 발생하는 레이아웃 이동(콘텐츠의 위치 혹은 크기변화)
- Opportunities
  - 페이지를 더 빨리 로드하는데 도움을 주는 제안
- Diagnostics
  - 속도와 직접적인 관계는 없지만 성능 향상에 도움을 주는 제안

## Performance panel (Google Chrome)
![performance-02.png](..%2F..%2Fassets%2Fimg%2Fperformance%2Fperformance-02.png)
- CPU Chart,
    - 노란색 : 자바스크릭트 실행
    - 보라색 : 랜러딩/레이아웃
    - 회색 : 기타 시스템 작업
    - 빨간색 선 : 병목 발생 지점(특정 작업이 Main Thread 소비)
- Network Chart
    - 네트워크 상태
- ScreenShot
    - 서비스 로드 순간 화면

![performance-03.png](..%2F..%2Fassets%2Fimg%2Fperformance%2Fperformance-03.png)
- Network Timeline
    - 오른쪽 회색선 : 초기 연결 시간
    - 막대의 옅은 색 영역 : 요청을 보낸 시점부터 응답을 기다리는 시점까지의 시간(TTFB)
    - 막대의 짙은 색 영역 : 콘텐츠 다운로드 시간
    - 오른쪽 회색 선 : 해당 요청에 대한 Main Thread의 작업 시간

![performance-04.png](..%2F..%2Fassets%2Fimg%2Fperformance%2Fperformance-04.png)
- Frames
    - 화면의 변화가 있을 때 Screenshot

![performance-05.png](..%2F..%2Fassets%2Fimg%2Fperformance%2Fperformance-05.png)
- Timings
    - User Timing API를 통해 기록된 정보
    - 리액트에서 각 컴포넌트의 랜더링 시간 표시

![performance-06.png](..%2F..%2Fassets%2Fimg%2Fperformance%2Fperformance-06.png)
- Main
    - 브라우저의 Main Thread 에서 실핼되는 작업을 플레임차크(Flame Chart) 로 표시

![performance-07.png](..%2F..%2Fassets%2Fimg%2Fperformance%2Fperformance-07.png)
- Summary
    - 선택 영영에서 발생한 작업 시간의 총합과 각 작업이 차이하는비중
- Bottom-Up
    - 가장 최하위에 있는 작업무터 가장 상위 작업까지 Tree View
- Call Tree
    - 가장 상위의 작업 부터 하위 작업까지 Tree View
- Event Log
    - 발생한 이벤트 목록

# Optimization  
## 이미지 사이즈 최적화 : Properly size images
- 이미지 사이즈가 너무 커서 렌더링에 오래 걸림.
- 1200px X 1200px -> 120px X 120px 로 변환 필요
- 하지만 레티나 디스플에이 해상도를 고려하여, 2배 사이즈로 랜더링할 필요 있음.
- 서버내 정적 이미지 파일
  - 파일 재작업
- API를 통해 들어온 이미지 파일
  - 이미지 CDN을 통한 재작업
    - > CDN (Content Delivery Network)
      > 
      > 물리적 거리이 한계를 극복하기 위해 사용자와 가까운 곳에 Contents 서버를 두는 기술
      > 
      > 이미지 CDN은 이미지를 사용자에게 보내기 전에 특정 형태로 가공하여 전해주는 기능도 추가됨.

## 병목 코드 최적화 : Reduce Javascript execution time
- ArticleList 병목 현상
  - 문자열 Replace 함수 최적화 필요
    - 반복문으로 처리하지 않고, 정규표현식으로 한꺼번에 처리한다.

## 코드분할 기법 적용
  - 페이지별로 코드를 분할하여 로딩
  - 해당 코드가 필요해진 시점에 로드뢰는 지연로딩 사용
  - 페이지 별로 코드분할
  - 모듈별로 코드 분할
  - React 에서는 Suspense, lazy 로 지연 로딩이 적용 가능함.

## 텍스트 압축 : Enable text compression
- 기본적으로 HTML, CSS, Javascript 는 텍스트 파일의 기반이기 때문에(Compile 된 파일이 아님) 텍스트 압축 기법을 사용할 수 있다.
- > 압축 방식
  > 
  > Gzip : 블록화, 휴리스틱 필터링, 헤더와 체크섬과 함게께 내부적으로 Deflate 를 사용
  > 
  > Deflate : L777이라는 알고리즘과 허프만 코딩을 사용하여 데이터를 감싸는 압축 방식
  > 
  > 압축률 : Gzip > Deflate
- > serve - Static file serving and directory listing
  >
  > USAGE
  >
  >     $ serve --help
  >     $ serve --version
  >     $ serve folder_name
  >     $ serve [-l listen_uri [-l ...]] [directory]
  >
  >     By default, serve will listen on 0.0.0.0:3000 and serve the
  >     current working directory on that address.
  >
  >     Specifying a single --listen argument will overwrite the default, not supplement it.
  >
  > OPTIONS
  >
  >    --help                              Shows this help message
  >
  >    -s, --single                        Rewrite all not-found requests to `index.html`
  >
  >    -u, --no-compression                Do not compress files

