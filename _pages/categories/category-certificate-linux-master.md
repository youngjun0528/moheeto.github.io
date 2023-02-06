---
title: "Certificate"
layout: archive
permalink: /categories/linux_master
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.linux_master %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}