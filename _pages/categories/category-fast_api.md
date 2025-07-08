---
title: "FastAPI"
layout: archive
permalink: categories/fast_api
author_profile: true
types: posts
---

REST API 를 쉽게 만들 수 있는 python 기반의 프레임워크인 FastAPI 에 대한 내용을 정리합니다. 

{% assign posts = site.categories['fast_api']%}
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
