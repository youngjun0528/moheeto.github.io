---
title: "Agile"
layout: archive
permalink: /categories/agile
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.agile %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}