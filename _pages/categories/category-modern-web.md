---
title: "Modern Web"
layout: archive
permalink: /categories/modern-web
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.modern-web | sort: "title" %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}