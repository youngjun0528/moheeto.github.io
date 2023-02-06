---
title: "Programming"
layout: archive
permalink: /categories/programming
author_profile: true
sidebar_main: true
---

## Java
{% assign posts = site.categories.java %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}

## Python
{% assign posts = site.categories.pyton %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}

## Data Structure
{% assign posts = site.categories.data_structure %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
