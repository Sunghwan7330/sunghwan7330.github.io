---
type: posts
time: "2014.03 ~ 2014.10"
title: "하이브리드 암호화 기반 보안 CCTV"
skills: [Java, Android(SDK, NDK), openCV]
description: ""
image: ""
company: "Gachon Univ"
categories: [project]
---

## 개요 

* 2014 가천대학교 인터랙티브미디어학과 졸업프로젝트 

## 기간

* 2014.03 ~ 2014.10

## 기술 

* Java, Android(SDK, NDK), openCV

## 업무

* Java 기반 Socket 서버 구현
* Android 기반 Socket 클라이언트 어플리케이션 구현
* AES 알고리즘 내부 수정 및 적용
* Hybrid 암복호화 모듈 설계 및 구현

## 시스템 구성도 

![image](https://user-images.githubusercontent.com/35713051/121362576-27229f80-c971-11eb-922c-f5d353e9c245.png)

* 서버와 클라이언트는 하이브리드 암호화 기반 암호화 키를 생성하여 보안 통신을 수행함
* 서버는 웹캠으로부터 입력되는 영상을 실시간으로 암호화하여 클라이언트에게 전송하며, 클라이언트는 암호화 영상을 복호화하여 스마트폰의 화면에 나타냄
* 서버는 카메라 영상을 이용하여 침입자 탐지를 수행할 수 있으며, 침입자 검출시 카메라 화면을 캡쳐하여 저장하고, 클라이언트에게 PUSH 메시지를 전송함

## 구현 결과 

![image](https://user-images.githubusercontent.com/35713051/121362630-31449e00-c971-11eb-86a1-cdc596455d66.png)
