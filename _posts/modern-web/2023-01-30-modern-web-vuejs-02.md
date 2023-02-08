---
title: "02 - Vuex 상태관리"
description: 
published: true
date: 2021-09-03T08:54:56.858Z
tags: 
- modern-web
- FrontEnd
- Vue.js
editor: markdown
dateCreated: 2021-09-03T03:35:56.147Z
categories: 
- vuejs
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "02 - Vuex 상태관리"
---

# 상태관리
## Web Application 의 상태관리
1. 전통적인 웹 애플리케이션
사용자가 다른 페이지로 이동할 때에는 Client에서는 상태가 사라진다.
애플리케이션의 상태는 Server에서 동적으로 생성되기 때문에 대부분의 상태는 Server 쪽에서 유지한다.

![screenshot_20210903-174744~2.png](..%2F..%2Fassets%2Fimg%2Fvuejs%2Fscreenshot_20210903-174744~2.png)

2. SPA(Single Page Application)
페이지의 새로고침이 존재하지 않음.
따라서, Back-end의 일부 로직을 Front-end로 이동하여 일부 상태를 관리하게 되어, Front-end 측에 Server 단과 유사하게 MVC모델을 도입하여 처리하게됨.
하지만, 이러한 상태관리를 위한 데이터 모델에 반응성을 제공하지만, 흐름을 제어 할 수 없다는 단점을 가지고 있다.
이로인해, 대규모 Application 같은 경우 데이터 흐름의 비동기성이 발생하여 데이터 모순이 발생할 가능성이 존재한다.

3. Flux
데이터의 흐름을 단방향으로 강제

![screenshot_20210903-174759~2.png](..%2F..%2Fassets%2Fimg%2Fvuejs%2Fscreenshot_20210903-174759~2.png)

## Vuex
컴포넌트 간 공유되는 상태를 위해 중앙 징중화된 Store로 활용
예측 가능한 방식으로만 변경한다.

![screenshot_20210903-174811~2.png](..%2F..%2Fassets%2Fimg%2Fvuejs%2Fscreenshot_20210903-174811~2.png)

