---
type: posts
title: "github blog를 검색포털에 검색되도록 설정하기"
excerpt: "github blog가 구글 네이버 등에 검색이 허용되도록 작업해보도록 합시다."

categories:
  - blog
tags:
  - minimal-mistakes
  - github 블로그

toc: true
toc_sticky: true

date: 2021-06-13
last_modified_at: 2021-06-13
---

# 개요 

github 블로그는 기본적으로 구글과 같은 검색포털에서 검색이 되지 않는다는 말을 들었다. 
그래서 검색포털에 검색이 되려면 몇가지 작업을 해줘야한다. 
오늘은 그 작업들을 해보려고 한다. 

# sitemap 생성 

블로그 레퍼지토리의 최상단 경로에 sitemap.xml 파일을 생성한 뒤 아래의 내용을 작성해준다. 

```
---
layout: null
---
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd" xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  {% for post in site.posts %}
    <url>
      <loc>{{ site.url }}{{ post.url }}</loc>
      {% if post.lastmod == null %}
        <lastmod>{{ post.date | date_to_xmlschema }}</lastmod>
      {% else %}
        <lastmod>{{ post.lastmod | date_to_xmlschema }}</lastmod>
      {% endif %}

      {% if post.sitemap.changefreq == null %}
        <changefreq>weekly</changefreq>
      {% else %}
        <changefreq>{{ post.sitemap.changefreq }}</changefreq>
      {% endif %}

      {% if post.sitemap.priority == null %}
          <priority>0.5</priority>
      {% else %}
        <priority>{{ post.sitemap.priority }}</priority>
      {% endif %}

    </url>
  {% endfor %}
</urlset>
```

해당 페이지를 등록하면 blog주소/sitemap.xml에 접속시 등록한 xml 화면이 나타나게 된다. 

# feed 생성

블로그 레퍼지토리의 최상단에 feed.xml 파일을 생성한 뒤 아래의 내용을 작성해준다. 

```
---
layout: null
---
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    <title>{{ site.title | xml_escape }}</title>
    <description>{{ site.description | xml_escape }}</description>
    <link>{{ site.url }}{{ site.baseurl }}/</link>
    <atom:link href="{{ "/feed.xml" | prepend: site.baseurl | prepend: site.url }}" rel="self" type="application/rss+xml"/>
    <pubDate>{{ site.time | date_to_rfc822 }}</pubDate>
    <lastBuildDate>{{ site.time | date_to_rfc822 }}</lastBuildDate>
    <generator>Jekyll v{{ jekyll.version }}</generator>
    {% for post in site.posts limit:30 %}
      <item>
        <title>{{ post.title | xml_escape }}</title>
        <description>{{ post.content | xml_escape }}</description>
        <pubDate>{{ post.date | date_to_rfc822 }}</pubDate>
        <link>{{ post.url | prepend: site.baseurl | prepend: site.url }}</link>
        <guid isPermaLink="true">{{ post.url | prepend: site.baseurl | prepend: site.url }}</guid>
        {% for tag in post.tags %}
        <category>{{ tag | xml_escape }}</category>
        {% endfor %}
        {% for cat in post.categories %}
        <category>{{ cat | xml_escape }}</category>
        {% endfor %}
      </item>
    {% endfor %}
  </channel>
</rss>
```

# robots.txt

블로그 레퍼지토리의 최상단에 `robots.txt` 파일을 생성해준다. 
이 파일은 검색 엔진이 홈페이지를 크롤링할 떄 사용된다고 한다. 
`robots.txt` 파일에 아래의 내용을 추가해준다. 

```
User-agent: *
Allow: /

Sitemap: http://jinyongjeong.github.io/sitemap.xml
```

# 사이트 등록

(진행중)

