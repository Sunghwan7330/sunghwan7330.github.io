---
title: "PORTFOLIO"
layout: single
permalink: /portfolio/
author_profile: true
sidebar:
  nav: "docs"
---

# Skill

* Programming language
  * C / C++ / Java / Python / ETC ...
* Development tools
  * gcc / gdb / valgrind / JDK / Android SDK, NDK / SQLite / cmake / VScode / VIM / ETC ...
* SCM
  * git / github / gitlab / Redmine
* Development platform 
  * Windows / Linux / Android / Raspberry pi / Arduino

# Experience

| Time | Company | Description |
| --- | ----- | ----- |
| 2018.04 ~ Current | WINS | IPS Developer | 
| 2015.03 ~ 2015.11 | Gigalane | Android Application Developer | 

# Project

<table>
    <tr>
        <th> Title </th>
        <th> Company </th>
        <th> Skills </th>
        <th> Time </th>
    </tr>
    {% for portfolio in site.portfolio reversed %}

    {% if portfolio.categories contains "project" and portfolio.categories.size == 1 %}
    <tr>
        <td>
            {% assign content = portfolio.content | strip_newlines %}
            {% if content != ""  or portfolio.redirect_to %}
                <a href="{{ portfolio.url }}">{{ portfolio.title }}</a>
            {% else %}
                {{ portfolio.title }}
            {% endif %}
        </td>
        <td>{{ portfolio.company }}</td>
        <td>{{ portfolio.skills | join: ", " }}</td>
        <td>{{ portfolio.time }}</td>
    </tr>
    {% endif %}
    {% endfor %}
</table>

# Personal Projects

<table>
    <tr>
        <th> Title </th>
        <th> Skills </th>
        <th> Time </th>
    </tr>
    {% for portfolio in site.portfolio reversed %}

    {% if portfolio.categories contains "project" and portfolio.categories contains "personal" %}
    <tr>
        <td>
            {% assign content = portfolio.content | strip_newlines %}
            {% if content != ""  or portfolio.redirect_to %}
                <a href="{{ portfolio.url }}">{{ portfolio.title }}</a>
            {% else %}
                {{ portfolio.title }}
            {% endif %}
        </td>
        <td>{{ portfolio.skills | join: ", " }}</td>
        <td>{{ portfolio.time }}</td>
    </tr>
    {% endif %}
    {% endfor %}
</table>

# Publication

<table>
    <tr><td>
        <b>[Master Thesis]</b>
        <a href="http://www.riss.kr/link?id=T14740899">A Study for the Data Security and Transmission Efficiency Enhancement method on the Cloud and Multi Device Environment</a>
        Master Thesis. University of Gachon, GyeoungGi, South Korea, (2018)
    </td></tr>
    <tr><td>
        <b>Sunghwan Jo</b>*,  and Gitae Han,
        <a href="https://www.kci.go.kr/kciportal/ci/sereArticleSearch/ciSereArtiView.kci?sereArticleSearchBean.artiId=ART002318584">"Secure Certificates Duplication Method Among Multiple Devices Based on BLE and TCP"</a>
        <i>KIPS Transactions on Computer and Communication Systems</i>, vol. 7, pp.49-58, 2018.
    </td></tr>
    <tr><td>
        <b>Sunghwan Jo</b>*,  and Gitae Han,
        <a href="https://www.kci.go.kr/kciportal/ci/sereArticleSearch/ciSereArtiView.kci?sereArticleSearchBean.artiId=ART002264361">"A Partial Encryption Method for the Efficiency and the Security Enhancement of Massive Data Transmission in the Cloud Environment"</a>
        <i>KIPS Transactions on Computer and Communication Systems</i>, vol. 6, pp.397-406, 2017.
    </td></tr>
    <tr><td>
        <b>Sunghwan Jo</b>*,  and Gitae Han,
        <a href="http://www.iraj.in/journal/journal_file/journal_pdf/12-401-151143857375-79.pdf">"Methods of Data Encryption and Key Management for the Enhancement of Security in an Cloud Environment"</a>
        <i>International Journal of Advances in Electronics and Computer Science, ISSN</i>, vol. 4, pp.2393-2835 2017.
    </td></tr>
    <tr><td>
        <b>Sunghwan Jo</b>*,  Seonghoon Kim, Yoonyoung Moon and Gitae Han,
        <a>"The Falsification Detection Method of Encrypted Transmission Data based on Electronic Signature in Zigbee Environment"</a>
        <i>한국스마트미디어학회 & 한국전자거래학회 2016 춘계학술대회 논문집</i>, vol. 4, pp.258-261 2016.
    </td></tr>
    <tr><td>
        <b>Sunghwan Jo</b>*,  Seonghoon Kim, Hyoseung Lee and Gitae Han,
        <a href="https://www.dbpia.co.kr/journal/articleDetail?nodeId=NODE06603107">"Data Communication Method Based on Hybrid Encryption in Smartphone"</a>
        <i>2014년 한국컴퓨터정보학회 하계학술대회 논문집</i>, vol. 22, pp.71-74 2014.
    </td></tr>
</table>

# Awards 
<table>
    <tr>
        <th> Title </th>
        <th> Time </th>
    </tr>
    {% for portfolio in site.portfolio reversed %}
        {% if portfolio.categories contains "award" %}
        <tr>
            <td>
                {% assign content = portfolio.content | strip_newlines %}
                {% if content != ""  or portfolio.redirect_to %}
                    <a href="{{ portfolio.url }}">{{ portfolio.title }}</a>
                {% else %}
                    {{ portfolio.title }}
                {% endif %}
            </td>
            <td>{{ portfolio.time }}</td>
        </tr>
        {% endif %}
    {% endfor %}
</table>

