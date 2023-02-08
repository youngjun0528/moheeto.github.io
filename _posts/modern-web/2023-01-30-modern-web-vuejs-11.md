---
title: "11 - Computed"
description: 
published: true
date: 2021-08-11T03:17:16.976Z
tags: 
- modern-web
- FrontEnd
- Vue.js
editor: markdown
dateCreated: 2021-05-10T06:54:49.129Z
categories: 
- vuejs
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "11 - Computed"
---

# computed
- 지속적인 연산, 꼐산이 필요한 데이터 표현
- Template에서 data, props와 사용법 동일, 스크립트에서 function 형태로 작성
- watch 보다 간편

```html
<template>
  <div class="hello">
    <a v-bind:href="today"> go today! </a>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      score: 0
    }
  },
  computed: {
    today(i){
      let date = new Date();
      return '/day/' + date.getDay();
    }
  }
};
</script>
```