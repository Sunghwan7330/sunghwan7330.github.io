---
type: posts
title: "minimal-mistakes sidebar 에 카테고리 추가 작업"
excerpt: "minimal-mistakes 테마에서 카테고리별로 글이 분류될 수 있도록 sidebar를 추가해봅시다! "

categories:
  - blog
tags:
  - minimal-mistakes
  - blog 커스텀

toc: true
toc_sticky: true

date: 2021-06-10
last_modified_at: 2021-06-10
---

# 개요 

블로그에 글을 쓰면서 카테고리별로 나누고 싶었다. 
좌측 sidebar에 카테고리별로 글을 모으고 싶어 여기저기 검색하여 삽질을 시작하였다. 

# 작업 내역

## _data/navigation.yml 에 내용 추가 

_data/navigation.yml 파일 하단에 다음과 같은 내용을 추가한다. 

```
docs:
  - title: Book Study
    children:
      - title: "임베디드 C프로그래밍 최적화"
        url: /categories/embedded_c_optimization
  - title: 삽질일기
    children:
      - title: "라즈베리파이"
        url: /categories/raspberry_pi
```

* title에는 카테고리의 큰 분류의 이름을 적음
* children의 title 에는 소분류의 이름을 적음
* children의 url 에는 해당 카테고리 페이지의 url을 적음

위와 같이 docs 라는 항목으로 카테고리를 추가하였다. 

만약 Book Study 에 카테고리를 더 추가하고 싶다면 children 아래에 title과 url 을 추가하면 된다. 

## 카테코리 페이지 추가 

_pages/categories 디렉토리에 카테고리별로 페이지를 추가하였다. 

아래는 예시로 '임베디드 C프로그래밍 최적화' 카테고리의 페이지를 작성하였다. (category-embedded_c_optimization.md)

```
---
title: "임베디드 C 프로그래밍 최적화"
layout: archive
permalink: categories/embedded_c_optimization
author_profile: true
types: posts
---
{% raw %}
{% assign posts = site.categories['embedded_c_optimization']%}
{% for post in posts %}
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}
{% endraw %}

```

* title : 페이지 제목을 입력
* layout : 페이지의 layout 입력
* permalink : 해당 페이지의 url 입력

이후 하단의 `assign posts = site.categories['embedded_c_optimization']` 을 보면 posts 에 카테고리가 embedded_c_optimization 인 글을 저장하게 된다. 
카테고리 명은 왠만하면 공백없는 영어를 추천한다. (제가 삽질하다 결국 바꿈...ㅠ)

이후 반복문을 통해 `archive-single.html` 의 형식으로 글을 뿌려주게 된다. 

## _config.yml 수정

default 항목에 post type 부분을 수정해준다.

```
defaults:
  # _posts
  - scope:
      path: ""
      type: posts
    values:
      layout: single
      author_profile: true
      share: true
      related: true
      sidebar:
        nav: "docs"
```

여기서 주목할 부분은 sidebar 항목인데, sidebar에 `nav`를 `docs`로 입력해주었다. 이렇게 하면 좌측 사이드바에 `navigation.yml` 에 입력한 docs 항목이 추가된다.

수정이 완료되었다면 수정항목을 commit한 뒤 push 하도록 하자 

