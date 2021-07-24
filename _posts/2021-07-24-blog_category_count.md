---
type: posts
title: "sidebar 에 카테고리에 개시글 카운트 추가"
excerpt: "minimal-mistakes 테마의 카테고리에 게시글 카운트 추가작업!"

categories:
  - blog
tags:
  - minimal-mistakes
  - blog 커스텀
  - category

toc: true
toc_sticky: true

date: 2021-07-24
last_modified_at: 2021-07-24
---

# 개요 

이전에 sidebar에 카테고리 추가 작업을 진행하였습니다. 

이 카테고리 옆에 게시글 카운트가 없으니 조금 허전한 느낌이 들었습니다. 

그래서 이번에는 카테고리별 게시글 카운트를 추가해보려 합니다. 

# 작업 내역

## `_data/navigation.yml` 에 카테고리 내용 추가 

이전에 `_data/navigation.yml` 에 docs 항목을 추가한 뒤 title과 url을 추가하였습니다. 
이번에는 이 부분에 `category`를 추가해주도록 합니다. 

```
docs:
  - title: Book Study
    children:
      - title: "임베디드 C프로그래밍 최적화"
        url: /categories/embedded_c_optimization
        category: "embedded_c_optimization"
  - title: 내용 정리
    children:
      - title: "라즈베리파이"
        url: /categories/raspberry_pi
        category: "raspberry_pi"
      - title: "gdb"
        url: /categories/gdb
        category: "gdb"
```

위와 같이 기존에 작성한 내용에 `category` 를 추가해줍니다. `category`에는 실제 해당 포스트에 입력한 카테고리의 이름을 적어주시면 됩니다. 

##  `_includes/nav_list` 커스텀하기 

`_includes/nav_list` 에서 카테고리명을 나열하는 부분의 코드는 아래와 같이 되어있습니다. 

```
{% raw %}
    {% for nav in navigation %}
      <li>
        {% if nav.url %}
	@@ -25,7 +24,15 @@
        {% if nav.children != null %}
        <ul>
          {% for child in nav.children %}
            <li><a href="{{ child.url | relative_url }}"{% if child.url == page.url %} class="active"{% endif %}>{{ child.title }}</a></li>
          {% endfor %}
        </ul>
        {% endif %}
      </li>
    {% endfor %}
{% endraw %}
```

`for child in nav.children` 의 반복문을 돌면서 카테고리의 이름을 출력해주게 됩니다. 
이 부분에서 카테고리 글 카운트가 추가될 수 있도록 아래와 같이 변경해주었습니다. 

```
{% raw %}
    {% for nav in navigation %}
      <li>
        {% if nav.url %}
	@@ -25,7 +24,15 @@
        {% if nav.children != null %}
        <ul>
          {% for child in nav.children %}
            <li><a href="{{ child.url | relative_url }}"{% if child.url == page.url %} class="active"{% endif %}>{{ child.title }}
						<!-- 카테고리별 글 수 추가-->
						{% for category in site.categories %}
              {% if child.category == category[0]%}
                ({{category[1].size}})
              {% endif %}
            {% endfor %}

            </a></li>
          {% endfor %}
        </ul>
        {% endif %}
      </li>
    {% endfor %}
{% endraw %}
```

가운데 하나의 for문을 더 추가하였습니다. 
해당 부분에서 카테고리명과 일치하는 부분의 글 수를 받아오도록 하였습니다. 

