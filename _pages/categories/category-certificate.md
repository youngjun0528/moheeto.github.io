---
title: "Certificate"
layout: archive
permalink: /categories/certificate
author_profile: true
sidebar_main: true
---

## SQLD
{% assign posts = site.categories.sqld %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}

## Linux Master
{% assign posts = site.categories.linux_master %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}

## ADsP
{% assign posts = site.categories.adsp %}
{% if posts %}
  {% assign posts = posts | sort: 'title' %}
{% endif %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
