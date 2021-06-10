---
title: "아두이노를 이용한 전등 원격제어 프로젝트"
excerpt: "누워있다가 졸릴때 전등의 불을 바로 꺼보고 싶었습니다. 그렇게 시작된 프로젝트!"

categories:
  - 'personal_project'
tags:
  - 아두이노
  - IoT

toc: true
toc_sticky: true

date: 2017-04-25
last_modified_at: 2021-06-10
---

## 개요

누워서 쉬던 도중 잠이 오는 경우가 있다. 
그래서 불을 끄려고 하면 일어나자마자 잠이 깨버리게 되어 고통스럽다. (저는 그렇습니다)
그 귀찮음에서 프로젝트가 시작되었다. 

방안에 굴러다니던 아두이노를 보고 
이걸 이용하여 어떻게 전들을 제어할지 생각하게 되었다. 

## 구현 방안 정리 

릴레이 모듈을 이용해서 전들을 제어해보려는 생각을 했다. 
하지만 전등처럼 전압이 쎈 곳에 릴레이 모듈을 연결하는게 무서워서 시도하지 않았다. 
(전기 무섭...ㅠㅠ)
따라서 다른 방법을 생각해보도록 했다. 

이후 전등 스위치를 물리적으로 제어해보겠다는 생각을 했고, 
서보모터에 긴 막대를 연결하여 스위치를 왼쪽/오른쪽으로 누를수 있도록 하여 
전등 스위치를 제어하기로 하였다. 

원격으로 서브모터를 제어하기 위해서는 아두이노에서 신호를 받아야 하는데, 
리모컨을 이용한 IR 신호와 블루투스 페어링을 이용한 방법으로 신호를 전달하도록 하였다. 

## 구현 결과 

<iframe src="https://www.facebook.com/plugins/video.php?height=476&href=https%3A%2F%2Fwww.facebook.com%2Fseonghwanj2%2Fvideos%2F1397487380319652%2F&show_text=false&width=380&t=0" width="380" height="476" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowfullscreen="true" allow="autoplay; clipboard-write; encrypted-media; picture-in-picture; web-share" allowFullScreen="true"></iframe>


## 구현 후기 

리모컨을 이용한 IR 신호 전달은 꽤나 잘 작동하였다. 

블루투스 패어링을 이용한 제어 방법도 역시나 꽤나 잘 동작하였다. 

다만 이 두가지 방법을 동시에 지원하게 했을때, 블루투스의 수신 딜레이로 인해 IR 신호를 제대로 받을 수 없었다. 

그래서 둘중 한가지 방법만 선택했어야 했다. 

블루투스의 경우 사용시 매번 패어링을 해줘야 하는 불편함이 있었다. 
가까워지면 자동으로 하게 할수도 있지 않을까 했지만.... 거기까지는 시도하지 않았다. 

그냥 누워있다가 졸리면 옆에있는 리모컨을 눌러 바로 불을 끌 수 있는게 더 편하게 느껴져서 IR 수신 기능만 적용하였다. 
