---
title: "Modern Web"
layout: archive
permalink: /categories/modern-web
author_profile: true
sidebar_main: true
---

## Agile
{% assign posts = site.categories.agile %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}

## Spring
{% assign posts = site.categories.spring %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}

## Vue.js
{% assign posts = site.categories.vuejs %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}

## Front-End Performance
{% assign posts = site.categories.performance %}
{% if posts %}
{% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}