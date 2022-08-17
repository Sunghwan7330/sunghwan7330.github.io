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

# 반사체를 이용한 DRDoS 원리 

해당 장에서는 편의상 DNS 서비스로 예시를 진행하겠습니다. 

## DNS 요청 / 응답 

DNS 는 Domain Name Server 의 약자입니다. 
DNS 서버는 인터넷 주소를 IP로 변환해주는 역할을 하게됩니다. 

예를 들어 인터넷 브라우저 주소창에 인터넷 주소를 입력하게 되면 DNS 를 통해 IP 주소로 바꿔주게 됩니다. 

[그림]

## IP spoofing

IP spoofing 은 IP를 속이는 공격방법입니다.

우리가 인터넷을 사용할 때 일반적으로는 IP 기반의 통신을 하게 됩니다. 
이때 주고받는 네트워크 패킷에는 IP header를 포함하고 있습니다. 

[그림 : IP header 구조]

위에 보이는 이미지가 IPv4 헤더의 구조입니다. 
여기서 Source IP는 보내는 대상의 IP 가 입력되고, Destination IP는 받는 대상의 IP가 입력됩니다. 

IP spoofing 은 source IP를 변조하여 보내는 대상의 IP를 숨기는 공격방법입니다. 
이렇게 하면 받는 대상자에게 보낸 대상자를 속일 수 있게 됩니다. 


