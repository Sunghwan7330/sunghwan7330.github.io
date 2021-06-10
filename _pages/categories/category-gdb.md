---
title: "gdb"
layout: archive
permalink: categories/gdb
author_profile: true
types: posts
---

많은 글이 올라올지는 모르겠지만... gdb를 사용하면서 정리할 사항을 적어보겠습니다. 

{% assign posts = site.categories['gdb']%}
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
