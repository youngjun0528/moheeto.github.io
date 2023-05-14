---
title: "12 - Form"
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
toc_label: "12 - Form"
---

# Form

## text
{% raw %}
```html
<input v-model="message" placeholdere="여기를 수정해보세요.">
<p> Msg : {{message}}</p>
```
{% endraw %}

## checkbox
{% raw %}
```html
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{checked}}</label>
```
{% endraw %}

## radio
{% raw %}
```html
<input type="radio" id="radio_one" value="1" v-model="picked">
<label for="radio_one">One</label>
<input type="radio" id="radio_two" value="2" v-model="picked">
<label for="radio_two">Two</label>
<span>{{picked}}</span>
```
{% endraw %}

## select
{% raw %}
```html
<select v-model="selected">
  <option disabled value="">Please Select One</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<span>{{selected}}</span>
```
{% endraw %}