# 03. SPA 앱의 구조

## Template 구조와 컴포넌트

1. Base
    - MV VM (..ViewModel)
        * View = dom, html
        * Model = data, store
    - 데이터 바인딩과 전달
    - 상태(데이터)에 의한 UI제어
    - 관심사의 분리
    - 분리와 재사용
      ![component](https://s3.ap-northeast-2.amazonaws.com/grepp-cloudfront/programmers_imgs/learn/course4672/component.png)

2. 기본 템플릿 구조
    - node_modules/ : npm 라이브러리
    - public/
        * index.html : 브라우저에 보여지는 페이지
    - src/
        * assets/ : Static 파일(image, css 등)
        * components/
            * HellowWorld.vue : Vue 컴포넌트
        * views/ : Vue 정적 단일 Tempalte
        * App.vue
        * main.js : Main. Vue 인스턴스 생성, 각종 모듈 IMPORT
        * router/
            * index.js : 앱 라우터 설정
        * store/
            * index.js : vuex. 상태광리 모듈 설정
    - package.json : npm 설정


