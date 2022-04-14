---
title: "임베디드 C를 위한 TDD"
layout: archive
permalink: categories/embedded_c_tdd
author_profile: true
types: posts
---

C 언어를 예제로 하는 TDD 를 찾던 중 추천받은 서적입니다. 
일반적으로 개발을 할때 코드를 먼저 작성한 뒤 테스트를 통해 디버깅을 합니다. 
하지만, TDD는 테스트를 먼저 작성한 뒤 이를 통과하기 위한 코드를 작성합니다. 

해당 서적에서는 TDD 의 장점을 소개하고, TDD 개발 방법에 대해 소개하려 합니다. 

{% assign posts = site.categories['embedded_c_tdd']%}
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
