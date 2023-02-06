---
title: "07 - Binding"
description: 
published: true
date: 2021-07-01T07:06:02.006Z
tags: 
- modern-web
- FrontEnd
- Vue.js
editor: markdown
dateCreated: 2021-04-19T22:20:10.356Z
categories: 
- vuejs
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "07 - Binding"
---

# Class, Style Binding
## v-bind:class
```html
<div :class="{active: isActive}">활성화</div>
```
* result
```html
# IF isActive False
<div class="">활성화</div>
# IF isActive True
<div class="active">활성화</div>
```

## v-bind:style
```html
<div v-bind:style="{color: activeColor, fontSize: fontSize + 'px'}"></div>
```
* result
```html
# IF activeColor is red and fontSize is 16
<div style="color:red;font-size:16px"></div>
```

{% raw %}
```html
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>

    <div :class="{active: isActive}">활성화</div>

    <div v-bind:style="{color: activeColor, fontSize: fontSize + 'px'}"></div>

  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      msg: "반갑습니다!",
      isActive: true,
      activeColor: 'red',
      fontSize: 16
    };
  },
};
</script>
```
{% endraw %}
