---
title: "04 - Performance"
description: 
published: true
date: 2023-04-24T07:06:23.366Z
tags: 
- modern-web
- FrontEnd-Performance
editor: markdown
dateCreated: 2023-04-24T07:06:23.366Z
categories: 
- performance
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "04 - Performance"
---

# 최적화 항목
> - 레이아웃 이동 피하기
> - 이미지 지연 로딩
> - 리덕스 렌러링 최적화
> - 병목 코드 최적 

# Tools
## React Developer Tools(Profiler)
웹 페이지 랜더링하는 과정에서 어떤 코드가 실행되었는지 확인
각 파일의 코드가 얼마나 실행되었는지 비율 확인
![performance-11.png](..%2F..%2Fassets%2Fimg%2Fperformance%2Fperformance-11.png)

# Optimization
## 레이아웃 이동 피하기
- 이미지가 삽입될 레이아웃이 빈번하게 변경이 이루어 진다면 성능의 문제가 발생한다.
- 레이아웃 이동의 원인
  - 사이즈가 미리 정의되지 않은 이미지 요소
  - 사이즈가 미리 정의되지 않은 광고 요소
  - 동적으로 삽입된 콘텐츠
  - 웹 폰트(FOIT, FOUT)
- 각 요소의 크기를 예측할 수 있다면 미리 사이즈를 지정하여 공간을 확보한다.
  - case 1. padding을 이용하여 box를 만든뒤 그 안에 이미지를 absolute로 지정
  - case 2. aspect-ratio CSS 속성

## 이미지 지연 로딩(react-lazyload)

```javascript
import LazyLoad from 'react-lazyload'

function Component() {
    return (
        <div>
            <LazyLoad offset={1000}>
                <img src='img_path' />
            </LazyLoad>
        </div>
    )
}
```

## 리덕스 렌더링 최적화
- 리엑트 
  - 렌더링 사이클 
    - 서비스 상태가 변경되면 화면에 반영하기 위해 리렌더링 과정을 거친다.
  - 리덕스
    - 서비스에서 사용하는 이미지 리스트와 헤더틔 카테고리, 그리고 모달에 관한 정보 관리
    - 컴포넌트들은 이 리덕스를 통해서 변경 감지 후 리렌더링 과정을 진행
    - Example) useSelector
      - 서로 다른 상태를 참조할 때에는 리렌더링 하지 않도록 구현
      - 판단 기준은 인자로 넣은 함수의 반환 값
        - 객체를 새로 만들지 않도록 반환값 나누기
          - 객체 반환 값을 나누면 특정 속성이 변경되더라도 전체 객체가 변한건 아니므로 리렌더링을 방지 
        - 새로운 Equality Function 사용
          - 객체가 기존과 동일한 지 판단하여 리랜더링 방지

## 병목 코드 최적화
- 메모이제이션으로 코드 최적화
  - 한번 실행한 함수에 대해 해당 반환 값을 기억해 두고 있다가 똑같은 조건으로 실행되었을 때 함수의 코드를 모두 실행하지 않고 바로 전에 기억에 둔 값을 반환 하는 기숭
  - ex.) 제곱함수 메모이제이션 적용 
  ```javascript
  const cache = {} // 함수의 반환값을 저장하기 위한 변수
  
  function square(n) {
    if (cache[n]) { // 이미 저장된 값이라면 기존 값을 그대로 반환
        return cache[n] 
    }
    const result = n * n;
    cache[n] = result; // 다음에 저장된 값을 사용할 수 있도록 저장
    return result
  }
  ```