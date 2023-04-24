---
title: "02 - Performance"
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
toc_label: "02 - Performance"
---

# 최적화 항목
> CSS 애니이메션 최적화
> 
> 컴포넌트 지연 로딩
> 
> 컴포넌트 사전 로딩
> 
> 이미지 사전 로딩

# Optimization
## 애니메이션 최적화
### 브라우저 랜더링 과정 
- 주요 랜더링 과정(Critical Rendering Path) or 픽셀 파이프라인(Pixel Pipeline)
  - DOM + CSSOM > 랜덤 트리 > 레이아웃 > 페인트 > 컴포지트
  - DOM + CSSOM
    - HTML, CSS 파일 다운로드
    - HTML Parsing -> DOM(Document Object Model) 생성
    - CSS Parsing -> CSSOM(CSS Object Model) 생성
  - 랜더 트리(Render Tree)
    - DOM과 CSSOM 결합 후 DOM 요소 포함 여부 결정
    - display:none - 랜더 트리 제외
    - opacity:0 or visibility:hidden - 랜더 트리 포함
  - 레이아웃(Layout)
    - 화면의 구성 요소의 위치나 크기 계산 후 배치 작업
  - 페인트(Paint)
    - 화면에 배치된 색을 결정 (배경색, 글자색 등)
    - 여러 개의 분할된 레이어(Layer)로 작업
  - 컴포지트(Composite)
    - 페인트 단계에서 나누어진 레이어를 합치는 작업
- 애니메이션 작업이 수행되면 앞서 진행한 브라우저 랜더링 과정을 다시 시작함.(Reflow, Repaint)
  - Reflow
    - 어떤 요소의 가로나 세로(margin, padding)가 변경되는 경우 발생
    - DOM + CSSOM 부터 다시 작성
    - position, display, width, float, height, font-family, top, left, font-size, font-weight, line-height, min-height, margin, padding, border 등
  - Repaint
    - 어떤 요소의 배경이나 색이 변경되는 경우 발생
    - 레이아웃 단계 제외
    - background, background-image, background-position, border-radius, border-style, box-shadow, color, line-style, outline 등
- transform, opacity 속성을 사용하는 경우 하드웨어 가속(GPU 가속)에 위임하여 처리하여 레이아웃과 페인트 단계를 건너뜀
  - transform: translate() : 처음부터 레이어를 분리하지 않고 변화가 일어나는 순간 레이어 분리
  - transform: translate3d() or scale3d(), will-change 속성은 사전에 레이어를 분리
  - 레이어를 너무 많이 분리하면 메모리 사용량이 늘어남.

## 컴포넌트 지연 로딩(lazy loading)
- 모달 팝업에 사용되는 이미지 갤러리 라이브러리는 첫 페이지에서는 사용되지 않으므로 제외
- React 에서는 Suspense, lazy 로 지연 로딩이 적용 가능함.
- 단점
  - 페이지 초기 화면에서는 페이지 로딩속도가 빨라지지만, 모달 팝업이 보여주는 시점에서는 여전히 지연로딩이 발생함.

## 컴포넌트 사전 로딩(Preloading)
- 컴포넌트 실행 전에 미리 로드를 해주는 기법
- Case 1 : 사용자가 버튼위에 마우스를 Over 하였을 때 (mouseenter)
    - onMouseEnter 이벤트 발생시 모달 Component 를 Import 한다.
- Case 2 : 최초에 페이지가 로드되고 모든 컴포넌트의 마운트가 종료되었을 때 (componentDisMount)
    - useEffect 함수로 모든 페이지가 로딩되면 모달 Component 를 Import 한다.

## 이미지 사전 로딩
- 모달 팝업 내 이미지 사이즈가 너무 커서 로딩이 느려지는 현상이 발생할때 사용하는 기법
  - useEffect 함수로 모든 페이지가 로딩되면 모달내 이미지 URL를 사전에 다운로드한다.
  - 얼마나 많은 이미지를 사전에 로드할 것인지 결정 필요