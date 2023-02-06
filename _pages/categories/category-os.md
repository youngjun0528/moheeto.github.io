---
title: "OS"
layout: archive
permalink: /categories/os
author_profile: true
sidebar_main: true
---

## Docker
{% assign posts = site.categories.docker %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
