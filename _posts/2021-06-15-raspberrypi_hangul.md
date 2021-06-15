---
title: "라즈베리파이 한글 깨짐 현상 해결"
excerpt: "라즈베리파이 초기 OS 설치 이후 xwindow 접속시 한글이 깨지는 현상이 있습니다. 이를 해결해보려 합니다. "

categories:
  - 'raspberry_pi'
tags:
  - raspberry pi
  - 라즈베리파이
  - 한글설정

toc: true
toc_sticky: true

date: 2021-06-15
last_modified_at: 2021-06-15
---


초기 OS 설치 후 locale 설정 등등 초기설정하고난 뒤 한글이 깨지는 현상이 있습니다.  

![](https://images.velog.io/images/jjump/post/a898cbaf-2307-433e-ba32-31c658d70a42/image.png)

기본적으로 한글 폰트가 설치되지 않은 것으로 보입니다. 
아래의 명령어를 입력하여 폰트 설치를 해줍시다 .

```
sudo apt-get fonts-unfonts-core
sudo apt-get fonts-unfonts-extra
sudo apt-get install ibus
sudo apt-get install ibus-hangul
```

이후 재부팅을 하게 되면 한글이 정상적으로 출력되게 됩니다. 

![](https://images.velog.io/images/jjump/post/b66baae6-ab21-470e-9cb6-419d6d7a6369/image.png)

