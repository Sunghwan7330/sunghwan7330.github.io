---
type: posts
time: "2017.06 ~ 2016.11"
title: "클라우드 기반 데이터 암복호화 시스템"
skills: [Jquery, Android NDK, ECC]
description: ""
image: ""
company: "Gachon Univ"
categories: [project]
---

## 기간

* 2016.06 ~ 2016.11

## 기술 

* Java / JSP / ECC / Google Drive API / Android NDK / MySQL

## 업무

* Android 기반 Hybrid App 개발
* Android 환경에서의 Micro SD 기반 데이터 암·복호화 모듈 구현
* Apache Tomcat 기반 웹 서버 구현
* MySQL 기반 데이터베이스 구현
* ECDH 기반 대칭키 생성 알고리즘 설계 및 구현

# 소개

![image](https://user-images.githubusercontent.com/35713051/121358416-926a7280-c96d-11eb-846b-9e5ae97c570f.png)

1. 송신자는 전송할 파일을 암·복호화 Micro SD 카드를 이용하여 암호화함
1. 송신자는 암호화된 파일을 구글 드라이브에 업로드함
1. 송신자는 송수신 전용 앱서버에 파일명, 파일 접근 URL, 암호화 키, 수신 대상자를 전송함
1. 수신자는 송수신 전용 앱서버에서 파일 정보를 수신함
1. 수신자는 구글 드라이브 저장소에서 암호화된 파일을 다운로드함
1. 수신자는 암·복호화  Micro SD 카드를 이용하여 파일을 복호화함

# 구현 결과 

![image](https://user-images.githubusercontent.com/35713051/121358668-c0e84d80-c96d-11eb-8505-491a001e58e7.png)
