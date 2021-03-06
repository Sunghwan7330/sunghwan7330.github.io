---
title: "[밑바닥부터 만드는 컴퓨팅] 10장 컴파일러 1 : 구문 분석"
excerpt: "해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 10장 내용을 정리하였습니다. "

categories:
  - 'make_computing_system'
tags:
  - 밑바닥부터 만드는 컴퓨팅
  - 컴파일러
  - 구문 분석

toc: true
toc_sticky: true

date: 2022-03-31
last_modified_at: 2022-03-31
---


# 1. 배경 

일반적인 컴파일러는 구문 분석과 코드 생성의 두 모듈로 구성된다. 

구문 분석 작업은 대개 두 모듈로 더 나눠집니다. 
하나는 토큰화 모듈로, 입력 문자들을 언어 기본 요소들로 분류하는 모듈입니다. 
또 하나는 파싱 모듈로 토큰화 결과로 나온 언어 기본 요소를 언어의 구문 규칙에 맞추는 모듈입니다.

![image](https://user-images.githubusercontent.com/35713051/162741734-4ea2a584-789a-48ae-9ac1-0c1fa98b6f7f.png)

## 1.1 어휘 분석 

프로그램의 가장 단순한 구문 요소는 텍스트 파일에 저장된 문자열입니다. 

프로그램 구문 분석의 첫 단계는 주석이나 공백을 무시하고 문자들을 토큰으로 분류하는 것입니다. 
프로그램이 토큰화되면, 토큰들은 프로그램의 기본 원소가 됩니다. 

![image](https://user-images.githubusercontent.com/35713051/162741777-a1568fec-c5fa-421a-ae4d-b71ab2f68c62.png)

## 1.2 문법 

프로그램을 어휘 분석해서 토큰 스트림으로 만들고 나면, 이제 토큰 스트림을 파싱하여 형식 구조로 만들어야 합니다. 
즉, 토큰들이 변수 선언, 명령문, 표현식 등과 같이 언어 구조 중에 어디에 해당하는지를 알아야 합니다. 

![image](https://user-images.githubusercontent.com/35713051/162741803-aab6a03a-cd34-4841-93b9-36c1d02181d5.png)

위 그림은 C언어의 규칙 일부와 이 문법에 맞는 코드 예시입니다. 

## 1.3 구문 분석

문법에 따라 입력 텍스트가 유효한지 확인해보는 행위를 구문분석이라고 합니다. 
문법 규칙이 계층적이기 때문에 parser가 생성하는 출력은 parse tree나 derivation tree 라고 불리는 트리 기반 데이터 구조로 기술됩니다. 

![image](https://user-images.githubusercontent.com/35713051/162741840-9bdc40b8-3e90-480b-9d4c-fe3c64f1f14c.png)

위 그림은 문법 조각에 따른 프로그램 조각의 파스 트리입니다.
삼각형은 더 아래 단계의 파스트리를 뜻합니다. 
