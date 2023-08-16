---
title: "클린코드"
layout: archive
permalink: categories/cleancode
author_profile: true
types: posts
---

개발자 필독도서로 꼽히는 도서 중 하나입니다.
정말 유익한 내용이 많은 책이라고 생각됩니다.

{% assign posts = site.categories['cleancode']%}
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
