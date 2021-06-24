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
last_modified_at: 2021-06-24
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

## google 검색 허용 작업

[Google Search Console](https://search.google.com/search-console/welcome) 에 접속합니다. 

![image](https://user-images.githubusercontent.com/35713051/122642916-d157af80-d147-11eb-960e-e9aff4a563f8.png)

위 이미지와 같이 URL 접두어에 자신의 블로그 주소를 입력해줍니다. 
그러면 다음과 같은 소유권 확인 다이얼로그가 나타납니다. 

![image](https://user-images.githubusercontent.com/35713051/122642958-fc420380-d147-11eb-94d2-f0ee97ab9bca.png)

다이얼로그에 포함된 html 파일을 다운로드합니다. 
이후 해당 파일을 블로그 레퍼지토리의 최상단 경로에 업로드한 후 push해줍니다.

이 작업은 도메인에 대한 소유권을 인증하는 작업이라고 합니다. 사이트의 소유자만이 구글 검색에 대한 허가를 해줄 수 있기 때문입니다. 

파일 업로드 후 push 한 뒤 약 1~2분 뒤 완료 버튼을 눌러줍니다. 
정상적으로 파일이 업로드되었다면 아래와 같은 화면이 나타나게 됩니다. 

![image](https://user-images.githubusercontent.com/35713051/122643065-999d3780-d148-11eb-8df0-0451dd8942e0.png)



(진행중)


## naver 검색 허용 작업 

[네이버 웹마스터 도구](https://searchadvisor.naver.com/) 에 접속한 뒤 로그인해줍니다. 
이후 우측 상단에 웹마스터 도구를 클릭합니다. 

![image](https://user-images.githubusercontent.com/35713051/123256491-fe330a80-d52b-11eb-93c3-1a9baf08ff08.png)

사이트 등록하는 화면에서 자신의 블로그 주소를 입력해줍니다. 

![image](https://user-images.githubusercontent.com/35713051/123256589-199e1580-d52c-11eb-8265-9dc6a1bd39af.png)

그러면 위의 구글 검색 작업했던것과 같이 소유권 증명을 해달라고 합니다. 
구글에서 햇던것과 같이 `1. HTML 확인 파일을 다운로드합니다.` 의 확인 파일을 받은 뒤 자신의 블로그 레퍼지토리 최상단 루트에 업로드해줍니다. 
 
push를 한 뒤 약 1~2분 뒤 소유 확인 버튼을 눌러주면 됩니다. 
