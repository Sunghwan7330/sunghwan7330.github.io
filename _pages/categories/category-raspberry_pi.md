---
title: "라즈베리파이"
layout: archive
permalink: categories/raspberry_pi
author_profile: true
type: posts
---

{% assign posts = site.categories['raspberry_pi']%}
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
