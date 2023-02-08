---
title: "05 - Component"
description: 
published: true
date: 2021-07-01T07:05:44.171Z
tags: 
- modern-web
- FrontEnd
- Vue.js
editor: markdown
dateCreated: 2021-04-12T06:49:16.570Z
categories: 
- vuejs
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "05 - Component"
---

# Component Structure
### template
최상위 DOM은 하나만 가지고 있어야 한다.
HTML + vue Template 문법

### Script
import
component 
data
method
component life cycle

### Style
css, sass
image src

###
- {% raw %} {{dataname}} {% endraw %} 으로 정의된 data를 가져와서 보여줌
- HTML 형식을 보여줄 때에는 v-html 지시자 사용
- 3항 연산자 사용 가능
- 연산자를 사용하여 숫자현 연산 또는 문자열 결합 가능

{% raw %}
```html
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>{{ author + 10 }}</h2>
    <p>age : {{ age + 10 }}</p>
    <p v-html="article"></p>
    <p>{{ seen ? "yes" : "no" }}</p>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      msg: "반갑습니다!",
      author: "bob",
      age: 30,
      article: "<strong>Hello</strong> world",
      seen: true,
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
```
{% endraw %}