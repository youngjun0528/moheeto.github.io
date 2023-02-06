---
title: "Jenkins"
layout: archive
permalink: /categories/jenkins
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.jenkins %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
