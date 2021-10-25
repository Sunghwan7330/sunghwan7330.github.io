---
title: "밑바닥부터 만드는 컴퓨팅 시스템"
layout: archive
permalink: categories/make_computing_system
author_profile: true
types: posts
---

지난번 팀원들끼리 스터디를 할때 검토했던 책인데, 제목 그대로 밑바닥부터 보기 좋은것 같아 다시 보려고 합니다. 

이 책에는 실습 내용이 있는데, 실제로 밑바닥부터 만들 수 있도록 되어있습니다. 
논리게이트부터 가산기, 컴파일러, OS 까지 볼 수 있도록 되어있습니다. 

정말 밑바닥부터 실습으로 만들어가면서 체험하면서 익실 수 있을 것 같아 보게 되었습니다. 

{% assign posts = site.categories['make_computing_system']%}
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
