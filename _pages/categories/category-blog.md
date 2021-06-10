---
title: "Blog dev"
layout: archive
permalink: categories/blog
author_profile: true
types: posts
---

jekyll 블로그를 커스텀하면서 정리한 내용을 업로드해보려 합니다. 

{% assign posts = site.categories['blog']%}
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
