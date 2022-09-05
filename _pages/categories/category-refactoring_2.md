---
title: "리팩터링 2판"
layout: archive
permalink: categories/refactoring_2
author_profile: true
types: posts
---

리팩터링으로 유명한 해외 서적입니다. 

저는 자바스크립트를 사용하는 개발자는 아니지만, 책은 유명하기에 보고 있습니다. 

예제는 파이썬으로 바꾸어서 정리하려 합니다.

{% assign posts = site.categories['refactoring_2']%}
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
