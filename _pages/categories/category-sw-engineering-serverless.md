---
title: "Serverless"
layout: archive
permalink: /categories/serverless
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.serverless %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
