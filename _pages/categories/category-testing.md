---
title: "삽질일기"
layout: archive
permalink: categories/testing
author_profile: true
types: posts
---

이곳은 일기장 혹은 메모장저럼 삽질하는 기록을 작성하려고 합니다. 

다른 포스팅처럼 정리되지 않고 막 작성될 예정입니다. 
삽질을 하다보면 잘 될때도 있지만 안될때가 더 많죠. 
그리고 하려던게 완성되서 정리하려고 하면 무슨 작업을 어떻게 했는지 햇갈리게 됩니다. 

이 카테고리는 그걸 방지하기 위해 삽질한 기록을 쭉쭉 작성하려고 합니다. 
그리고 목표하던것이 완성되면 이곳의 글을 정리하여 다른 카테고리에 포스팅을 할 예정입니다.

 
{% assign posts = site.categories['testing']%}
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
