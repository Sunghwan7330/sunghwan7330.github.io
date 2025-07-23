---
title: "[FastAPI] 인증 및 인가 소개"
excerpt: "인증 및 인가에 대해 소개하고FastAPI 에서의 예시를 설명합니다."

categories:
  - 'fast_api'
tags:
  - FastAPI
  - auth

date: 2025-07-20
last_modified_at: 2025-07-20
---


# 인증이란?

* 사용자 또는 시스템이 자신이 **누구인지를** 증명하는 과정
  * ex) 웹사이트 로그인
  * ex) 앱 실행 시 지문 인식

## 지식 기반 인증

* 사용자만 유일하게 알고 있는 것을 이용하여 사용자를 확인하는 방법
  * ex) 패스워드, PIN 등
* 장점 : 구현 및 적용이 용이하고 익숙한 방식
* 단점 : 탈취 위험이 높음 (노출, 피싱, 브루트포스 공격)

## 소유 기반 인증

* 사용자가 소유한 물리적 또는 디지털 매체로 인증
  * OTP, 문자 인증, 보안 카드, usb 동글 
* 일반적으로 단독으로 사용하지 않고 2차 인증으로 사용

* 장점 : 비밀번호 없이 인증 가능
* 단점 : 분실 또는 도난의 위험이 있음


## 존재 기반 인증

* 사용자의 고유한 생체 정보를 기반으로 인증 
  * ex) 지문, 홍채, 얼굴 인식(Face ID), 정맥 등

* 장점 : 사용이 편의하고 위변조가 어려워 보안이 높음
* 단점 : 정보 유출시 변경이 불가하며 고비용 장비가 필요


# 인증 구현 방안

## OAuth

* `Open Authorization` 의 약자
* 사용자가 패스워드를 제공하지 않고 제 3자의 어플리케이션이 사용자의 자원에 접근할 수 있게 해주는 `인증 및 권한 위임 프로토콜`

  <img width="433" height="406" alt="image" src="https://github.com/user-attachments/assets/914c4ccc-5d3e-400b-b58c-f3ecd1c6affa" />


### OAuth 의 주요 개념

| 개념                               | 설명                                |
| -------------------------------- | --------------------------------- |
| **리소스 소유자 (Resource Owner)**     | 사용자 (User)                        |
| **클라이언트 (Client)**               | 사용자의 자원에 접근하고 싶은 애플리케이션           |
| **리소스 서버 (Resource Server)**     | 사용자 자원을 보관하는 서버 (예: Google Drive) |
| **인증 서버 (Authorization Server)** | 토큰을 발급해주는 서버                      |
| **액세스 토큰 (Access Token)**        | 제한된 권한을 가진 키. API 호출에 사용됨         |


### OAuth 권한 부여 코드 승인 방식

* OAuth 에서 가장 기본이 되는 방식

<img width="1538" height="944" alt="image" src="https://github.com/user-attachments/assets/df787193-0103-4c47-b458-380f3989631c" />

1. 리소스 접근 요청
    * 사용자가 클라이언트를 통해 리소스에 접근 시도
1. 인증 요청
    * 클라이언트가 인증 서버로 이동시켜 사용자 로그인 / 동의 화면 표시
1. Authorization Code 발급
    * 인증 서버가 콜라이언트에게 인가 코드를 전달
1. Access Token 요청
    * 클라이언트는 받은 인가 코드를 이용해 인증 서버에 토큰 요청
1. Access Token 응답
    * 인증 서버는 클라이언트에게 access token 을 발급
2. API 요청
    * 클라이언트는 access token을 이용해 리소스 서버에 사용자 자원 접근

* `Authorization Code` 를 발급받는 과정이 생략되고 `Access Token`을 바로 발급받는다면, 권한 부여 코드 요청 시 전달받은 redirect_uri을 사용해야 함
  * 이때 `Access Token` 이 브라우저에 노출됨
* `redirect uri`로 전달된 `Authorization Code`는 프런트엔드에서 백엔드로 전달
* 백엔드는 전달받은 `Authorization Code`를 가지고 `Authorization Server`의 API에 요청하여 `Access Token`을 발급함
* 백엔드 사이에서 `Access Token`이 전달되기 때문에 안전해짐

### OAuth 암묵적 승인 방식

* 자격 증명을 안전하게 저장하기 힘든 클라이언트에서 사용
* `Access token`이 uri를 통해 바로 전달되기 때문에 만료 기간을 짧게 설정해야 함

<img width="1131" height="626" alt="image" src="https://github.com/user-attachments/assets/77824327-b4b8-465c-9536-46de0964bffc" />

1. 리소스 접근 요청
    * 사용자가 클라이언트를 통해 리소스에 접근 시도
2. 인증 요청
    * 클라이언트가 인증 서버로 이동시켜 사용자 로그인 / 동의 화면 표시
3. Access Token 응답
    * 인증 서버는 클라이언트에게 `access token` 을 발급
4. API 요청
    * 클라이언트는 `access token`을 이용해 리소스 서버에 사용자 자원 접근
  
## JWT

* `Json Web Token` 의 약자
* 웹 어플리케이션에서 인증 및 권한 부여를 위한 토큰 기반 인증 방식
* `Json` 포맷의 정보를 URL-safe 하게 인코딩한 토큰
* 서버는 상태를 저장하지 않고 토큰만으로 사용자 정보를 확인할 수 있음
* `OAuth` 에서 `Access token` 발급 시 주로 JWT 를 사용

### JWT 구조

* JWT 는 3부분으로 구성되어 있으며 `.` 으로 구분됨
```
(Header).(Payload).(Signature)
```

* Header : 어떤 알고리즘으로 서명했는지 명시
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

* 페이로드 : 사용자 정보, 역할, 만료시간 등을 명시
```
{
  "sub": "1234567890",
  "name": "sunghwan",
  "role": "admin",
  "exp": 1721667600
}
```

* 서명 : Header + Payload 를 base64로 인코딩 후 비밀키로 서명
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

### JWT 장점

| 장점          | 설명                            |
| ----------- | ----------------------------- |
| Stateless | 서버는 세션 상태를 저장하지 않음 → 수평 확장 쉬움 |
| 빠른 인증 처리  | 요청마다 DB 조회 없이 토큰만으로 처리 가능     |
| 범용성       | 다양한 플랫폼/언어에서 쉽게 사용 가능         |
| 보안성 (서명)  | 서명을 통해 위조 여부 확인 가능            |


### JWT 단점

| 단점               | 설명                                         |
| ---------------- | ------------------------------------------ |
| 토큰 탈취 위험       | 노출 시 재사용 가능 → HTTPS 필수                     |
| 토큰 폐기 어려움      | JWT는 상태를 서버에 저장하지 않음 → **Blacklist 구현 필요** |
| 페이로드는 암호화되지 않음 | 누구나 디코딩 가능 → 민감 정보는 **절대 저장 금지**           |
| 토큰 크기 큼        | 세션 ID보다 크기 큼 → 네트워크 부하 약간 증가               |



# 인가란?

* 인증된 사용자에게 특정 자원에 접근할 수 있는 **권한**을 부여하는 것
* 인증이 먼저 수행된 후 인가를 판단함

## RBAC

* `Role-Based Access Control` 의 약자 
* `역할(role)`에 따라 접근 권한을 제어 
  * ex) 관리자 : 읽기 / 쓰기 / 삭제 가능
  * ex) 사용자 : 읽기 / 쓰기
* 권한을 간단하게 부여할 수 있음
* 역할이 많아질수록 복잡해짐

## ABAC

* `Attribute-Based Access Control` 의 약자
* 속성(Attribute) 에 따라 접근 제어
  * ex) 나이가 18세 이상이면 접근 허용
* 보다 세밀한 권한 제어 가능
* 복잡하고 정책 설계가 어려울 수 있음

## ACL

* `Access Control List` 의 약자
* 리소스마다 허용된 그룹의 목록을 명시적으로 지정
* 운영은 상세하지만 관리가 어려움

# API 에서 인증 / 인가

* 무분별한 접근 차단
  * 인증 / 인가 없이 누구나 API 호출시 문제점
    * 자동화 봇으로 트래픽을 유발하여 서버 자원 고갈
    * 브루트포스 공격으로 데이터 확인
  * 인증을 통해 사용자를 확인하고 제어
  * 인가를 통해 데이터 접근을 차단

* 민감정보 보호
  * API 는 종종 민감한 정보를 다룸
    * 사용자 정보 
    * 결제 정보 
    * 개인 정보
  * 인증이 된 사용자에게만 정보를 노출

* 감사 추적 및 로그 관리
  * 누가, 언제, 어떤 요청을 하였는지 기록하여 필요시 확인
    * 로그는 보안사고 추적에 핵심

# FastAPI 구현 예시 

* 아래의 저장소에서 예제 코드 확인
  * https://github.com/Sunghwan7330/my_study/tree/master/python/fastapi/jwt_test
