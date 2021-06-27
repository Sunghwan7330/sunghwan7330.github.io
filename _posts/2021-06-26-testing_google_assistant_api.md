---
title: "[삽질일기] 구글 어시스턴트 API 테스트"
excerpt: "구글 어시스턴트 API 테스트 사용을 위한 삽질"

categories:
  - 'testing'

date: 2021-06-26
last_modified_at: 2021-06-26
---

# 삽질 시작

https://console.actions.google.com/?pli=1

위 페이지 접속 후 프로젝트 등록

![image](https://user-images.githubusercontent.com/35713051/123507306-a3242380-d6a3-11eb-8548-27daef46827a.png)
`new project` 클릭

![image](https://user-images.githubusercontent.com/35713051/123507320-b3d49980-d6a3-11eb-8085-e8a4b3162038.png)

위 화면은 처음에만 나오는것 같습니다. 
다시 시도했을때는 나오지 않네요. 

![image](https://user-images.githubusercontent.com/35713051/123507370-f0a09080-d6a3-11eb-96d2-12decec6372f.png)

한국인,  한국 선택하고 등록 

![image](https://user-images.githubusercontent.com/35713051/123507390-00b87000-d6a4-11eb-96c0-7ba385d1701b.png)

일단은 스마트홈 선택하고 `Start Building` 클릭

![image](https://user-images.githubusercontent.com/35713051/123507420-2cd3f100-d6a4-11eb-88ca-0f78708142f2.png)

이 화면이 나타났는데, 일단은 여기까지! 



https://console.developers.google.com/apis/library/embeddedassistant.googleapis.com?pli=1

위 페이지 들어가서 구글 어시스턴트 활성화 

![image](https://user-images.githubusercontent.com/35713051/123507715-ee3f3600-d6a5-11eb-9f73-00c23dd9ac8c.png)

프로젝트를 조금전 만든걸로 바꾸고 활성화 클릭
(저는 이미 누른 뒤 캡쳐를 했네요)


![image](https://user-images.githubusercontent.com/35713051/123507452-66a4f780-d6a4-11eb-8766-991fc4df2a8b.png)

이런 대시보드가 나타나네요


OAUTH 클라이언트 ID 생성하기 

https://console.developers.google.com/apis/credentials/oauthclient?hl=ko

위 페이지 접속

![image](https://user-images.githubusercontent.com/35713051/123507774-52fa9080-d6a6-11eb-8581-8d5e0f054e85.png)

동의화면구성 클릭

갑자기 블루스크린뜨면서 캡쳐한 부분이 날라감....ㅠㅠ

기억을 살려보면

`외부` 를 선택하고 이메일정도만 입력한 뒤 `저장 후 계속`을 눌렀습니다. 


테스트를 위해 라즈베리파이에 라이브러리를 다운로드 받습니다. 

```
# sudo apt-get update
# sudo apt-get install python3-dev python3-venv
# mkdir googleassistant_test
# cd googleassistant_test

# python3 -m venv env

# env/bin/python -m pip install --upgrade pip setuptools --upgrade

```

Google Cloud Platform 에서 OAuth2.0 ID의 키를 다운받습니다. 

![image](https://user-images.githubusercontent.com/35713051/123546823-3ee38b80-d799-11eb-9e62-f4bddbb34bfd.png)
저기 다운로드 버튼 클릭!

이후 해당 파일의 내용을 저장해줍니다. 



가상환경에서 구글 어시스턴트 라이브러리 설치 및 샘플 설치 

```
# python -m pip install --upgrade google-assistant-library

# python -m pip install --upgrade google-assistant-sdk[samples]
```

파이썬 인증툴 설치 

```
# python -m pip install --upgrade google-auth-oauthlib[tool]
```

구글 인증 라이브러리 설치 

```
# google-oauthlib-tool --client-secrets credentials.json \
--scope https://www.googleapis.com/auth/assistant-sdk-prototype \
--scope https://www.googleapis.com/auth/gcm \
--save --headless
```

그러면 터미널에 특정 URL에 접속하라고 나타난다. 

해당 페이지에 접속해서 인증을 해야되는것 같은데....

![image](https://user-images.githubusercontent.com/35713051/123547039-1f992e00-d79a-11eb-8580-73edf5897d18.png)

에러가 났다...

오늘은 여기까지 ㅠㅠ



# 참고 사이트 

* https://e3jake.tistory.com/53
* https://m.blog.naver.com/chandong83/221081942490
