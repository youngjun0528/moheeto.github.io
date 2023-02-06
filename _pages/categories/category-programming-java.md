---
title: "Java"
layout: archive
permalink: /categories/java
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.java %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}

