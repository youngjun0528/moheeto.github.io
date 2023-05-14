---
title: "13 - Style"
description: 
published: true
date: 2023-02-08T15:56:00.000Z
tags: 
- modern-web
- FrontEnd
- Vue.js
editor: markdown
dateCreated: 2023-02-06T10:54:00.000Z
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