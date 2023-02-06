---
title: "10 - Method"
description: 
published: true
date: 2021-08-11T03:16:57.981Z
tags: 
- modern-web
- FrontEnd
- Vue.js
editor: markdown
dateCreated: 2021-05-10T06:33:38.057Z
categories: 
- vuejs
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "10 - Method"
---

# Method
- 주로 이벤트를 처리하기 위한 동작을 구현할 때
- 컴포넌트 data에 대한 접근은 [this.데이터명]으로 한다.

```html
<template>
  <div class="hello">
    <button v-on:click="update(1)">Button</button>
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
  methods: {
    update(i){
      this.score = i
      console.log('test : ' + i)
    }
  }
};
</script>

```