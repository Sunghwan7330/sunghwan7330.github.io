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

![image](https://user-images.githubusercontent.com/35713051/189895259-571b3061-ed3f-4304-8dc7-075c2b29a777.png)

대상자에게 많은 트래픽을 전송하기 위해 많은 공격 PC를 필요로 합니다. 
일반적으로는 봇넷, 좀비PC를 이용하여 공격을 수행합니다. 

## DRDoS

DRDoS는 외부의 정상적인 서버(반사체)를 이용하여 공격을 수행합니다. 

![image](https://user-images.githubusercontent.com/35713051/189895672-c9d18213-3f62-4568-9239-aa4a0036e06e.png)

DRDoS의 공격은 실제 서비스되고 있는 서버를 이용하기 때문에 공격의 추적이 어렵습니다. 

# 반사체를 이용한 DRDoS 원리 

해당 장에서는 편의상 DNS 서비스로 예시를 진행하겠습니다. 

## DNS 요청 / 응답 

DNS 는 Domain Name Server 의 약자입니다. 
DNS 서버는 인터넷 주소를 IP로 변환해주는 역할을 하게됩니다. 

예를 들어 인터넷 브라우저 주소창에 인터넷 주소를 입력하게 되면 DNS 를 통해 IP 주소로 바꿔주게 됩니다. 

![image](https://user-images.githubusercontent.com/35713051/189895893-91b2fae9-e5cd-467e-8c0a-7830049ca6f4.png)

## IP spoofing

IP spoofing 은 IP를 속이는 공격방법입니다.

우리가 인터넷을 사용할 때 일반적으로는 IP 기반의 통신을 하게 됩니다. 
이때 주고받는 네트워크 패킷에는 IP header를 포함하고 있습니다. 

![image](https://user-images.githubusercontent.com/35713051/189896295-db2c2a2e-eaf2-4494-8921-dceceeedfc91.png)

위에 보이는 이미지가 IPv4 헤더의 구조입니다. 
여기서 Source IP는 보내는 대상의 IP 가 입력되고, Destination IP는 받는 대상의 IP가 입력됩니다. 

IP spoofing 은 source IP를 변조하여 보내는 대상의 IP를 숨기는 공격방법입니다. 
이렇게 하면 받는 대상자에게 보낸 대상자를 속일 수 있게 됩니다. 


## 반사체를 이용한 서비스거부 공격

위에서 DNS 서버의 특징과 IP spoofing 에 대해 간단히 알아보았습니다. 
이제 DRDoS가 반사체를 이용하여 어떻게 공격하는지 감이 오실껍니다. 

DNS 서버는 요청에 대한 응답을 보내주게 됩니다. 
IP spoofing 을 통해 source IP를 변경하게 된다면....?? 
DNS 서버는 응답을 변경된 IP로 전달하게 됩니다. 

여러 DNS 서버에 IP spoofing 을 사용하여 요청을 보내게 된다면, DNS 서버를 이용한 DoS 공격이 가능해집니다. 
이떄 응답을 보내는 DNS 서버를 반사체라고 이해하시면 됩니다. 

![image](https://user-images.githubusercontent.com/35713051/189896216-6ed4d40e-ac85-4c80-89a5-414b8393686e.png)


# DRDoS 증폭 공격

앞에서 DRDoS 의 공격 방법에 대해서 알아보았니다. 

DRDoS의 경우 반사체를 이용해 트래픽을 발생시키는 공격입니다. 
이때 공격을 효율적으로 하기 위해서는 한번의 요청으로 많은 응답을 받도록 해야합니다. 
즉, 공격자는 조금의 트래픽만을 사용해서 많은 양의 트래픽을 유도하게 해야합니다. 

이와 같이 프로토콜의 특징을 이용하여 최대한의 서비스 응답을 받도록 하여 트래픽을 집중시키는 것을 증폭공격이라고 합니다. 

TCP는 handshake를 통한 연결 과정이 있기 때문에 일반적으로는 UDP 서비스를 이용하여 공격합니다. 
DRDoS 증폭공격에 주로 사용되는 프로토콜은 DNS, NTP, CLDAP, SNMP 등이 있습니다. 

아래는 각 프로토콜별로 요청에 대하여 응답이 얼마나 많이 증폭되는지에 대한 비율을 나타냅니다. 

![image](https://user-images.githubusercontent.com/35713051/189896387-5d34d17c-3b0f-438a-ac34-85045e4610d0.png)

# DRDoS 방어 기법

해당 절 부터는 저의 주관적인 의견이 있음을 알려드립니다. 

## UDP fragment 패킷 탐지 

DRDoS 공격자는 대부분 Amplification attack 을 사용합니다. 응답 패킷을 최대한 증폭시켜 보내개 됩니다. 

그렇기 때문에 대부분의 패킷은 fragment 형태로 전송되게 됩니다. 

하나의 대상으로 많은 fragment 패킷이 유입된다면 DRDoS 공격을 의심할 수 있습니다. 

하나의 서버에서만 응답한다면 일반적인 사용으로 판단할 수 있겠지만, 다수의 서버에서 큰 사이즈의 응답이 지속된다면 공격으로 볼 수 있습니다. 
이때 해당 IP를 잠시 차단하여 공격을 막을 수 있습니다. 

## UDP 세션 관리를 이용한 탐지 

일반적은 서버와 클라이언트의 통신은 요청과 응답으로 이뤄집니다. 
하지만 DRDoS 공격의 경우 응답만 전송되게 됩니다. 

UDP 세션 관리를 통해 요청이 없이 응답만 오는 경우 공격으로 탐지할 수 있습니다. 

요청이 없는데 서버로부터 응답이 지속된다면 해당 서버의 IP를 일시적으로 차단하여 공격을 방어할 수 있습니다. 

## 프로토콜 특징을 이용한 탐지 방법 

해당 방법은 각 프로토콜의 응답을 분석하여 공격을 탐지하는 방법입니다.

이 방법은 구글에 검색하면 많은 논문들이 있습니다. 

주로 DNS 또는 NTP를 이용한 Amplication attack 에 대한 탐지 방법이 있으며 방법도 다양합니다.
관심 있다면 한번 찾아보시는 것을 추천드립니다. 
