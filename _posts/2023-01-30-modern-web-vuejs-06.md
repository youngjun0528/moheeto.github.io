---
title: "06 - Directive(지시자)"
description: 
published: true
date: 2021-07-01T07:06:05.876Z
tags: 
- modern-web
- FrontEnd
- Vue.js
editor: markdown
dateCreated: 2021-04-19T23:03:13.816Z
categories: 
- vuejs
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "06 - Directive(지시자)"
---

# Directive(지시자)
## v-if
```html
<p v-if="seen">이제 나를 볼 수 있어요</p>
<p v-if="!seen">이제 나를 볼 수 없어요</p>
<p v-else>이제 나를 볼 수 없어요</p>
<p v-else-if="neverSeen">이제 나를 볼 수 없어요</p>
```
## v-show
```html
<p v-show="seen">이제 나를 볼 수 있어요</p>
<p v-show="!seen">이제 나를 볼 수 없어요</p>
```
* v-if와 v-show 의 차이점
v-if로 선언시 DOM 객체 자체가 아예 SHOW & DELETE 관계라면,
v-show 선언시 DOM 객체는 SHOW & HIDE 관계이다.
만약 숨겨진 DOM 객체에 대한 참조가 필요할 때, show 로 숨기기 처리만 하면 된다.

## v-bind
```html
<a v-bind:href="url">링크 이동 </a>
<img v-bind:src="imagePath" />
<a :href="url">링크 이동 </a>
<img :src="imagePath" />
```

{% raw %}
```html
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>

    <p v-if="seen">보인다</p>
    <p v-else>보이지 않는다</p>

    <p v-show="seen">Show</p>
    <p v-show="!seen">Not Show</p>

    <a v-bind:href="url">링크 이동 </a>

  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      msg: "반갑습니다!",
      seen: false,
      url: 'http://google.com'
    };
  },
};
</script>
```
{% endraw %}