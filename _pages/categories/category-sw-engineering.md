---
title: "Software Engineering"
layout: archive
permalink: /categories/sw-engineering
author_profile: true
sidebar_main: true
---

## TDD
{% assign posts = site.categories.tdd %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
