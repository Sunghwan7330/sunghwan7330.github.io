---
title: "DRDoS 살펴보기"
excerpt: "업무 중 DRDoS 에 대한 조사가 필요하여 블로그에 함꼐 내용을 정리하려 합니다. "

categories:
  - 'infomation'
tags:
  - DoS
  - DRDoS
  - UDP reflection attack
  - UDP Amplification attack

date: 2022-08-15
last_modified_at: 2022-08-15
---

# 개요 

직장에서 업무 중 DRDoS 에 대한 조사가 필요하여 자료를 검색하게 되었습니다. 
검색한 내용을 정리하여 포스팅을 진행해보려 합니다. 


# DRDoS

DRDoS는 Distributed Reflection Denial of Service 의 약자로 분산 반사 서비스 거부 공격이라는 의미입니다. 

대역폭을 점유하는 서비스 거부공격이라는 점은 DDoS와 동일합니다.

다만 DRDoS는 반사체를 이용하여 서비스 거부 공격을 수행한다는 특징이 있습니다.

# DDoS와 DRDoS 의 차이

## DDoS

DDoS 의 경우 일반적으로 대량의 공격 PC 를 이용하여 대상자에게 서비스 거부 공격을 수행합니다. 
공격을 하는 PC가 대상자를 향해 직접 트래픽을 전송하게 됩니다 

[그림]

대상자에게 많은 트래픽을 전송하기 위해 많은 공격 PC를 필요로 합니다. 

## DRDoS

DRDoS는 외부의 정상적인 서버(반사체)를 이용하여 공격을 수행합니다. 

[그림]

DRDoS의 공격은 실제 서비스되고 있는 서버를 이용하기 때문에 공격의 추적이 어렵습니다. 

