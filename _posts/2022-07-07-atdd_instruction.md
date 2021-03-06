---
title: "우아한 ATDD"
excerpt: "유튜브 '우아한 Tech' 채널의 '우아한 ATDD' 정리본입니다. "

categories:
  - 'infomation'
tags:
  - atdd
  - tdd
  - agile

date: 2022-07-07
last_modified_at: 2022-07-07
---

# 개요 

이번 포스팅은 `우아한 Tech` 채널에서 강의한 우아한 ATDD 에 대한 내용을 정리하려 합니다.

* https://youtu.be/ITVpmjM4mUE

이번 포스팅은 개조식으로 정리하겠습니다. 

대부분은 강의자료를 가져왔습니다. 

# 인수테스트 첫 만남 

* 해당 절에서는 인수테스트를 접했을 떄의 느낌을 정리함

##  새로운 팀 새로운 문화 

### 페어 프로그래밍 

* 즉각적인 피드백 받을 수 있었음 

### 테스트 

* 테스트 코드로 부터 받는 피드백
* 심리적인 안정감을 제공함 

### 인수 테스트 

* 사용자 스토리(시나리오) 기반으로 기능 테스트 

### 인수 테스트의 도움 

* 배표 없이 받는 빠른 피드백
* 새로운 팀의 도메인과 서비스 흐름 파악에 큰 도움이 됨 
* 도메인을 이해하는데 예상보다 짧은 시간이 소요
* 인수 테스트로 스펙 표현 가능 

### 빠른 피드백

* 새로운 문화들의 공통점은 빠른 피드백을 받는 방법
* 든든한 지원군과 함께 코딩을 하는 느낌을 받음 
* 자신감을 주는 자동화된 테스트 

###  피드백을 받는 방법 

* 페어 프로그래밍 
* 테스트 / 인수 테스트 
* 코드 리뷰 
* 배포 
* 출시 

* 이 중 즉각적인 피드백을 줄 수 있는 테스트가 효과적이라고 생각함 

### 테스트 주도 개발 

* 테스트를 설계 활동으로 바꾸는 효과도 있음 
* 이로 인해 설계 품질에 관한 피드백도 빠르게 받게 도와줌
* 기존에는 검증의 목적으로 테스트를 했다면, TDD는 좋은 설계를 할 수 있도록 도움을 줌

### 인수 테스트 주도 개발 

* 개발 전 인수 테스트 작성 
* 인수 테스트 통과를 위한 개발 

### 인수 테스트를 기반으로 개발을 할 경우 

* 기존 인수 테스트 장점 
  * 빠른 피드백을 받을 수 있음 
  * 회귀 오류를 잡아줄 꾸준한 테스트를 만들 수 있음 
  * 기존 기능을 망가뜨리지 않고 새 기능을 추가할 수 있음 
* 인수 테스트를 작성하면서 구현할 대상에 대한 이해도 증가 
* 작업의 시작과 끝이 명확해져서 심리적인 안정감에 도움을 줌 

# ATDD란 

## 테스트 vs TDD

![image](https://user-images.githubusercontent.com/35713051/180205881-8eff1643-bbb3-4986-98b2-c57408e6f31a.png)

* 테스트는 기존의 기능을 검증하는 용도 
*  TDD는 구현의 요구사항을 명세하기 위해 테스트를 사용함 

## TDD vs BDD

![image](https://user-images.githubusercontent.com/35713051/180205901-2eb19fdf-a488-4846-a9d3-c1048ad86e7d.png)

* BDD 는 행위를 기반으로 테스트를 작성해 나가는 방식

## TDD vs BDD vs ATDD

![image](https://user-images.githubusercontent.com/35713051/180205936-a0776ad5-aba5-4ea7-9c23-b6072ecd36e8.png)

* ATDD 는 요구사항의 명세를 인수테스트로 명세함

## 다 같이 인수 조건을 정의하는 이슈 

* 각자의 생각만으로 개발하면 기획자, 개발자, QA 전부 다른 기능을 생각할 수 있음 
* 개발 전 다 같이 인수 조건을 정의한 뒤 개발을 진행하는 것이 중요함 

## ATDD 프로세스 

![image](https://user-images.githubusercontent.com/35713051/180205956-9da61a38-215f-43ab-b7e1-850bde735696.png)

* TDD에 비해 복잡한 프로세스를 가짐
* 본 강의에서는 개발 관련된 내용만 다룸 

# ATDD 개발 프로세스 예시 

* 인수 조건 정의
* 인수 테스트 작성 
* 기능 구현 

## ATDD 개발 과정 예시 - 강의 수강 대기 신청 

* 수강 신청 서비스에서 수강 정원이 다 찼경우 신청자가 강의 포기시 대기자가 수강신청되는 방식

### 인수 조건 

* 인수 테스트가 충족해야 하는 조건 

```
given
  강사는 강의를 생성했다.
  강사는 강의를 신청 가능 상태로 변경하였다.
  강의 모집인원 만큼 신청을 받았다.

when
  회원이 수강 대기 신청을 요청한다.

then
  회원은 강의의 수강 대기자로 등록 되었다.
```



### 인수 테스트 

* 인수 조건을 검증하는 테스트 
* 실제 요청/응답하는 환경과 유사하게 테스트 환경을 구성

* 테스트 예시
![image](https://user-images.githubusercontent.com/35713051/180205995-cdce80f3-5438-43b0-b8c9-1822f9b939bf.png)

### 기능 구현 

* 인수테스트를 충족하기 위한 코드 구현 
* 기능 구현은 TDD로 진행할 수 있음

# ATDD 관련 경험한 고민들 

## 인수 테스트 유래 

* 인수 : 물건을 받는다 
* SW 개발을 의뢰하고 결과물을 인수받을 때 요구사항에 만족하는지 확인하는 테스트
* 개발 전 인수 조건을 확인하고 테스트를 만듦

## 인수 조건 만들기 

### 요구사항 (사용자 스토리)

* 예시 
  * 누가 - 왜 -  무엇을 순서로 작성
```
강사는 
강의료를 환불해주기 위해 
수강생의 수강을 취소 할 수 있다.
```

### 인수 조건 표현 

* given : 사전 조건
* when : 검증 대상 
* then : 기대 결과 

### 인수 조건 작성 

* 검증 하고자 하는 when 구문 먼저 작성 
* 기대 결과를 의미하는 then 구문 작성 
* when과 then에서 필요한 정보를 given을 통해 마련

### 인수 조건 예시 

```
given 
  수강생이 수강 신청을 하였다. 
  과정의 남은 기간이 절반 이상이다. 
when 
  강사는 특정 수강생의 수강 상태를 취소 요청을 한다.
then 
  특정 수강생의 수강 상태가 취소 된다. 
  특정 수강생의 결제 내역이 환불 된다.
```

## 인수 테스트 코드 작성하기 

### 인수 테스트 특징 

* Black Box 테스트 
  * 내부 구조나 작동과 연관이 없는 테스트 

### 테스트 도구 (웹)

* 테스트 서버(환경) 
  * @SpringBootTest 
* 테스트 클라이언트 
  *  MockMVC, WebTestClient, RestAssured

* 도구에 대한 내용은 생략 

### 인수 테스트 예시 

![image](https://user-images.githubusercontent.com/35713051/180206033-37f526cd-e1bd-47c6-8c45-487228754219.png)

## 테스트 초기화 

* 몇몇 예시들이 소개되지만 웹에서 사용할 수 있을만한 내용들임. 
* Application 래벨에서 테스트 구축시에는 테스트 하니스를 사용하는것이 좋을듯 함 

### repository 활용하여 데이터 초기화?

* 손쉽게 데이터 초기화가 가능 
* 구현이 달라지면 테스트 영향을 받음 
* 유효성 검사 로직 없음 
* 깨지기 쉬운 테스트가 될 가능성이 높음

### 요청을 통한 데이터 초기화 

* 테스트 객체를 이용하여 직접 호출 후 초기화 

![image](https://user-images.githubusercontent.com/35713051/180206072-8c6bda81-50b2-4ae5-be25-6083fd114913.png)

## 중복 처리 & 가독성 개선 필요 

* 테스트 코드 가독성의 중요성 
  * 가독성이 좋지 않으면 방치될 가능성이 높음 
  * 테스트는 간결하게 작성해야 함 

* 메서드 분리 
  * 반복되는 코드는 메서드로 분리 

* 다른 인수 테스트에서 재사용
  * 재사용을 위한 클래스 또는 함수로 분리 

## 인수 테스트 작성 팁 

* 간단한 성공 케이스 우선 작성 
  * 동작 가능한 가장 간단한 성공 케이스로 시작 
  * 테스트가 동작하면 더 좋은 생각이 떠오를 수 있음 

* 인수 테스트 클래스 
  * 같은 테스트 픽쳐스를 공유하는 인수 테스트를 클래스 단위로 묶음 

## 인수 테스트 다음 TDD

* 인수 테스트 작성 후 기능 개발을 TDD로 진행 
  * TDD로 하지 않을 경우 개발 후 피드백을 덜 받게 됨 

* 도메인 부터 TDD 
  * 인수 테스트를 통해 시나리오 및 전반적인 기능에 대한 기능을 이해 
  * 이후 핵심 기능에 대한 도메인 설계를 한 후 도메인 객체에 대한 TDD를 수행 

## 추천하는 흐름

* Top-Down으로 방향을 잡고, Bottom-Up으로 구현하기
  * 인수 테스트 작성을 시작으로 내부 구현을 TDD로 구현 
* 인수 테스트 작성을 통해 요구사항과 기능 전반에 대한 이해를 선행 
* 내부 구현에 대한 설계 흐름을 구상
* 설계가 끝나면 도메인부터 차근차근 TDD로 기능 구현 
* 만약 도메인이 복잡하거나 설계가 어려울 경우 이해하고 있는 부분 먼저 기능 구현
* 인수 테스트의 요청을 처리하는 부분부터 진행할 수 있음

# ATDD 도입해보기 

## 나혼자 ATDD

* 토이 프로젝트로 경험 쌓기 
* 간단한 기능부터 적용해보기 
* 경험해보면서 상황에 맞는 방법 찾기 

## 다함께 ATDD (개발팀)

* ATDD 개발 프로세스 룰 정하기(인수 조건, 포맷 등)
* 팀 회의와 회고를 통한 피드백
* 살아있는 규칙 정하기

### 성공적인 ATDD 적용을 위해

* 아주 쉽게 시작할 수 있는 부분부터 도입
* 조금씩 상황에 맞게 조정 
* 가벼운 프로세스부터 시작, 문제점 발견 시 민첩하게 대응

### 레거시 기반 인수 테스트 작성하기 

* 먼저 인수 테스트를 작성하여 기존에 구현된 기능을 보호하기

### 인수 테스트 작성 가이드 작성

![image](https://user-images.githubusercontent.com/35713051/180206143-65f0da5f-5846-44ad-a7fa-b6a68a1a38ae.png)

### 지속적인 피드백 

![image](https://user-images.githubusercontent.com/35713051/180206169-f5c9ce68-b4c9-4032-8f38-6cd3f63aeab6.png)

## 기획 & QA와 함께하는 ATDD

### 기획, QA 설득하기(장점 소개)

* 개발 친화적인 용어는 제외하고 설명하기 
* 기존 방식과 비교하여 장점 이야기 하기

### 다같이 만드는 요구 사항 

* 기획/개발/QA 함께 인수 조건 회의 참여 
* 화면 기반으로 작성할 경우 이해도가 높음 
* 모든 인수 조건을 다같이 만드는건 비효율적

###  ATDD 적용 전과 후, 장점과 단점 확인

*  Common Understaning 
  * 다른 포지션의 관점은 물론 업무 프로세스도 간접적으로 익힐 수 있음 
  * 다른 포지션의 진행 상황에 대한 인지와 이해도가 높아짐 
* 인수 조건 정의의 어려움 
  * 문서를 어떻게 관리해야 할 지에 대한 고민이 필요 
* 리소스 
  * (기획/개발) 두번 작업하는 느낌을 받음

# Q&A

## ATDD는 TDD의 레드 그린 사이클을 따라가되 테스트를 인수테스트로 하는가?

* 개발을 해 나갈때 인수 테스트 작성 후 개발 해 나가는 방식
* 기능 구현시 TDD의 레드 그린 사이클을 따라 개발함 

## 어떤 기능을 개발시 인수 테스트부터 작성하려 하면 TDD의 빠른 피드백이 희석될까?

* 인수 테스트가 작성 비용이 크고 피드백이 느리는건 사실 
* 하지만 ATDD 안에 TDD 가 포함됨 
  * TDD 개발시 레드 그린 사이클 에서 많은 유닛 테스트가 생성될 것 
* TDD 부터 시작하면 어디서부터 어떻게 해야되는지 막막할 수 있음 
* ATDD로 시작시 조금 더 시작하기 쉬울 것 

## 통합 테스트와 인수 테스트의 차이

* 인수 테스트는 사용자 스토리 기반으로 기능 테스트를 진행
* 통합 테스트는 각각 단위들이 모였을 때 잘 동작하는지 확인 
* 비슷하게 보이지만, 인수 테스트는 사용자 스토리 기반의 테스트라는 차이가 있음 

## TDD 도입도 어려운데 ATDD를 도입할 수 있을지?

* TDD보다 ATDD를 도입하는 것이 더 쉬울 것이라 생각됨
* 적용하기 쉬운 부분부터 서서히 도입하는게 좋음
* 이러한 관점에서 보았을 때는 TDD 보다는 ATDD가 도입하기 더 좋다는 의견 

## 관심 없는 구성원들에게 어떻게 기술을 전파할 수 있을지?

* 구성원의 마음을 변화하는 일은 쉽지 않음 
  * 실패하더라도 좌절하지 말 것 
* 토이프로젝트 등을 통해 자신의 실력을 키울 것 
  * 구성원을 바꾸기 위함도 있지만 나의 성장을 위해서도 좋음
* 본인이 잘 알고있을 때 전파하는 것이 좋음 
  * 남을 알려줄 때 신뢰감을 줄 수 있음 
