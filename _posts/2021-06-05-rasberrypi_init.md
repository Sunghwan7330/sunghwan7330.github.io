---
title: "라즈베리파이 초기 설정"
excerpt: "라즈베리파이 초기 OS 설치와 이후 기본적인 설정을 해보도록 합시다."

categories:
  - 'raspberry_pi'
tags:
  - raspberry pi
  - 라즈베리파이
  - vnc server
  - 라즈베리파이 초기설정
  - ssh

toc: true
toc_sticky: true

date: 2021-05-31
last_modified_at: 2021-05-31
---

# 개요

우연치않게 라즈베리파이와 터치디스플레이를 얻게 되었습니다. 
뭘 해볼지 고민하다가 집에서 사용할만한 장난감을 만들어보기로 했습니다. 
앞으로 뭘 어떻게 이용해먹을지는 차차 생각해나가기로 하겠습니다. 
우선적으로 하고싶은건 전자 시계와 집안 제어를 위한 어시스턴스를 만들어보고 싶습니다. (새로운 삽질이 되겠군요)
그리하여 라즈베리파이에 OS를 올려 구동하였습니다. 
앞으로 사용하기 위한 초기 설정을 해보도록 하겠습니다. 

# OS 설치 

OS 설치는 raspberry pi imager을 사용하였습니다.
해당 툴을 이용하면 OS를 편하게 올릴 수 있습니다. 
다운로드는 아래의 링크에서 가능합니다. 
* https://www.raspberrypi.org/software/

설치 후 실행하면 아래와 같은 화면이 나타납니다. 
![raspberrypi_imager](https://user-images.githubusercontent.com/35713051/120826609-9b39fd80-c595-11eb-81f8-51fc1d29cd59.png)

이 화면만 보셔도 벌써 감이 오실껍니다...만
모르시는 분들도 계실 수 있으니 간단하게 설명해드리도록 하겠습니다. 
화면에 보이는 버튼들의 기능은 각각 다음과 같습니다. 

* CHOOSE OS : 설치하고자 하는 OS를 선택할 수 있습니다. 
* CHOOSE STORAGE : OS를 설치할 드라이브를 선택합니다. 
* WRITE : OS 를 write 하는 작업을 수행합니다. 

참고로 이 글을 작성할때는 이미 OS 설치 후 라즈베리파이를 구동한 상태이기 때문에 sd 카드는 PC가 아닌 라즈베리파이에 들어있습니다. 따라서 sd 카드 관련 부분은 글로만 설명드리도록 하겠습니다. (다음부터 주의할께요 ㅠㅠ)

CHOOSE OS 버튼을 누르면 아래의 화면이 나타납니다. 
![이미지선택](https://user-images.githubusercontent.com/35713051/120826679-a9881980-c595-11eb-8837-f8e255c95b32.png)
OS의 종류가 다양하지만, 저는 기본적인 raspberry pi OS를 선택하였습니다. 

다음으로 CHOOSE STORAGE 버튼을 누르면 아래의 화면이 나타납니다. 
![디스크 선택](https://user-images.githubusercontent.com/35713051/120826698-ae4ccd80-c595-11eb-8e31-7cb939226f4e.png)

말씀드렸다시피 sd카드가 pi에 들어있습니다 ㅠㅠ
PC에 sd카드를 연결하면 위의 리스트에 나타나게 됩니다. 
나타나셨다면 sd카드를 선택해주시면 됩니다. 

이후 WRITE 버튼을 클릭하게 되면 sd 카드에 OS가 설치되게 됩니다. 
(이 부분도 이미 진행하여 이미지가 없네요 ㅜㅜ)


# 초기 ssh 활성화 및 wifi 설정 

이 역시 이미 세팅하였기 때문에 글로만 설명드리도록 하겠습니다. 

## ssh 활성화 

sd 카드에 OS write 후 최상위 경로에 파일명이 'ssh'인 파일을 생성하시면 됩니다. 
(파일만 생성하면 됩니다.)
![image](https://user-images.githubusercontent.com/35713051/120826743-b86ecc00-c595-11eb-802c-ae7a4ea3bbd2.png)

## wifi 설정 

sd 카드에 OS write 후 최상위 경로에 파일명이 'wpa_supplicant.conf'인 파일을 생성하시면 됩니다. 
이후 해당 파일에 아래의 내용을 입력하시면 됩니다. 
```
country=GB
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
  ssid="wifi 이름"
  psk="비밀번호"
}
```

ssid 에는 wifi 명을 입력하고, psk 에는 wifi 비밀번호를 입력하시면 됩니다. 

# ssh 접속 

저는 putty를 사용하여 ssh를 접속합니다. 
다른 프로그램을 사용하신다면 해당 터미널 프로그램으로 하셔도 됩니다. 

![putty](https://user-images.githubusercontent.com/35713051/120826786-c3296100-c595-11eb-9159-18589bccf16f.png)

putty를 실행하면 위와 같은 화면이 나타납니다. 
Host Name에 라즈베리파이의 IP를 입력한 뒤 하단의 Open 버튼을 클릭합니다. 
라즈베리파이의 IP는 공유기 설정을 통해 확인 바랍니다. 

라즈베리파이의 초기 접속 정보는 다음과 같습니다. 
* user : pi
* passwd : raspberry

ssh 접속 후 위의 접속정보를 입력하면 터미널에 접근이 가능합니다. 
![terminal](https://user-images.githubusercontent.com/35713051/120826934-e6eca700-c595-11eb-8df4-26307355cd3f.png)


# 비밀번호 변경

초기 비밀번호를 계속 사용하는 것은 보안상 매우 위험합니다. 
'passwd' 명령어를 입력하여 비밀번호를 변경해줍니다. 

```
pi@raspberrypi:~ $ passwd
```

명령어를 입력한 뒤 아래의 정보를 순서대로 입력해줍니다. 

* Current password: 현재 비밀번호
* New password: 변경할 비밀번호 
* Retype new password: 비밀번호 확인

올바르게 입력하였다면 'password updated successfully' 메시지가 출력되면서 비밀번호가 변경됩니다. 
![terminal_passwd](https://user-images.githubusercontent.com/35713051/120826853-d50b0400-c595-11eb-983d-162afd644a1f.png)


# VNC 활성화 

ssh 터미널에 아래의 명령어를 입력합니다. 

```
pi@raspberrypi:~ $ sudo raspi-config

```

그럼 아래와 같은 설정 화면이 나타납니다. 
방향키를 눌러 '3 Interface Options' 를 선택한 뒤 엔터를 누릅시다
![terminal_raspi_config_1](https://user-images.githubusercontent.com/35713051/120826976-eeac4b80-c595-11eb-8e03-701b75558d8f.png)


이후 'P3 VNC' 를 선택한 뒤 엔터를 누릅니다. 
![terminal_raspi_config_2](https://user-images.githubusercontent.com/35713051/120826988-f23fd280-c595-11eb-9633-4c6daf715fa6.png)


Yes를 눌러 활성화해줍니다. 
![terminal_raspi_config_3](https://user-images.githubusercontent.com/35713051/120827002-f53ac300-c595-11eb-8622-96f702b1dc0f.png)


VNC 서버 활성화 완료!
![terminal_raspi_config_4](https://user-images.githubusercontent.com/35713051/120827013-f835b380-c595-11eb-9572-b17869e2fd21.png)


VNC 해상도 설정도 해줍시다. 
vi를 이용하여 아래의 경로의 파일을 열어줍니다. 
```
pi@raspberrypi:~ $ vi /boot/config.txt
```
![vnc_config](https://user-images.githubusercontent.com/35713051/120827027-fcfa6780-c595-11eb-927b-78554a621d55.png)


아래의 옵션에 주석을 해제하고 희망하는 해상도를 입력해줍니다. 
```
framebuffer_width=1280
framebuffer_height=720
```
네트워크 속도에 따라 해상도가 너무 크면 느릴수 있으니 적당히 설정해줍니다.

해상도 설정이 끝났으면 라즈베리파이를 재부팅해줍니다. 
'reboot' 또는 'init 6' 을 입력하면 라즈베리파이가 재부팅됩니다. 

재부팅 후 netstat 명령어를 입력해봅시다. 
```
pi@raspberrypi:~ $ netstat -ant
```
![netstat](https://user-images.githubusercontent.com/35713051/120827055-01268500-c596-11eb-93a9-e9aa6298f92a.png)


TCP 5900 포트가 LISTEN 상태인 것을 확인할 수 있습니다. 
이제 VNC viewer 를 이용하여 접속해보도록 합시다. 

* https://www.realvnc.com/en/connect/download/viewer/

저는 무설치를 선호해서 'standalone x64' 로 다운받았습니다. 
만약 설치판을 받으신분은 설치 후 viewer를 실행해주세요.
(설치는 각자 알아서!!)
실행 후 상단에 '{IP주소}:5900' 을 입력해줍니다. 
![vnc_addr](https://user-images.githubusercontent.com/35713051/120827077-07b4fc80-c596-11eb-929a-360da47f4de7.png)


접속에 성공하면 아래와 같은 화면이 나타납니다. 
contiune를 클릭해줍니다. 
![vnc_connect](https://user-images.githubusercontent.com/35713051/120827088-0b488380-c596-11eb-991d-045fdaca7f68.png)


로그인 화면이 나타납니다. 
ssh 접속정보와 같이 username과 password를 입력해줍니다. 
![vnc_login](https://user-images.githubusercontent.com/35713051/120827097-0daadd80-c596-11eb-9c48-6e701ca281d5.png)


접속에 성공하면 라즈베리파이의 바탕화면이 나타나게 됩니다! 
![mainscreen](https://user-images.githubusercontent.com/35713051/120827124-126f9180-c596-11eb-8e7f-5e4a38f61485.png)

