---
title: "13 - Style"
description: 
published: true
date: 2021-08-11T03:17:55.976Z
tags: 
- modern-web
- FrontEnd
- Vue.js
editor: markdown
dateCreated: 2021-05-10T07:06:23.366Z
categories: 
- vuejs
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "13 - Style"
---

# Style
- 컴포넌트에 한정된(scoped) 스타일 적용
- inline과 src 모두 가능
- 독립된 컴포넌트, 관심사의 분리

```html
<style scoped>
  .day ul {padding: 10px;}
  .day ul li {padding: 5px;}
</style>
```

```html
<style lang='scss' sec='./assets/css/style.css' />
```