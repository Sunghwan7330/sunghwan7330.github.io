---
title: "애자일"
layout: archive
permalink: categories/agile
author_profile: true
types: posts
---

애자일에 대한 내용을 정리하는 공간입니다. :)

{% assign posts = site.categories['agile']%}
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
