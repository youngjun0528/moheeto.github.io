---
title: "08 - Loop"
description: 
published: true
date: 2021-08-11T03:16:18.997Z
tags: 
- modern-web
- FrontEnd
- Vue.js
editor: markdown
dateCreated: 2021-05-10T06:19:30.203Z
categories: 
- vuejs
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "08 - Loop"
---

# v-for

{% raw %}
```html
<template>
  <div class="hello">
    <ul>
      <li v-for="(item, index) in items" v-bind:key="item.name">
        {{index}}:{{item.name}}, {{item.age}}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      items:[
        {name:'red', age: 15},
        {name:'blue', age: 29},
        {name:'green', age: 32}
      ]
    };
  },
};
</script>
```
{% endraw %}
