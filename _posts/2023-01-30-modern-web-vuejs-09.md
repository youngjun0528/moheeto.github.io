---
title: "09 - Event"
description: 
published: true
date: 2021-08-11T03:16:38.719Z
tags: 
- modern-web
- FrontEnd
- Vue.js
editor: markdown
dateCreated: 2021-05-10T06:27:31.696Z
categories: 
- vuejs
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "09 - Event"
---

# v-on
```html
<template>
  <div class="hello">
    <button v-on:click="update(1)">Button</button>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  methods: {
    update(i){
      console.log('test : ' + i)
    }
  }
};
</script>
```

## Basic
```html
<button v-on:click="methodName">클릭하세요.</button>
<button @click="methodName">클릭하세요.</button>
<button @click="methodName(param)">클릭하세요.</button>
```

## Event 수식어
```html
<form @submit.prevent='onSubmit'></form>
```
* .stop
* .prevent
* .capture
* .self
* .once

## Event Key 수식어
```html
<input @keyup.enter='onSubmit'></input>
```
* .enter
* .tab
* .delete : "DELETE" 키와 "Backsapce" 키
* .esc
* .space
* .up
* .down
* .left
* .right
