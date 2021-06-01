---
title: "북스터디"
layout: archive
permalink: categories/book_study
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.['임베디드 C 프로그래밍 최적화']%}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
