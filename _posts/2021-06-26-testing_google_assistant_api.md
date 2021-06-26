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

밥먹고 다시 삽질하러 오겠습니다. ㅇㅁㅇ.....

# 참고 사이트 

* https://e3jake.tistory.com/53
* https://m.blog.naver.com/chandong83/221081942490
