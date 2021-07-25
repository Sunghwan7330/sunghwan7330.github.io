---
title: "라즈베리파이 samba 설정하기 "
excerpt: "라즈베리파이의 저장소에 쉽게 접근하기 위해 samba 설정을 해보도록 합시다."

categories:
  - 'raspberry_pi'
tags:
  - raspberry_pi
  - samba

date: 2021-07-11
last_modified_at: 2021-07-11
---


# 개요 

라즈베리파이와 윈도우 간에 쉬운 파일 공유를 위한 samba 설치를 하려고 합니다. 
저는 라즈베리파이에서 환경설정을 하였지만, 우분투에서도 동일하게 설치 및 설정이 가능할 것으로 생각됩니다. 

# samba 설치 

아래의 명령어를 입력하여 samba 설치를 진행합니다. 
```
pi@raspberrypi:~ $ sudo apt-get install samba samba-common-bin
```

아래의 화면이 나타나면 '예'를 선택하여 설치를 진행합니다.
![image](https://user-images.githubusercontent.com/35713051/125196824-3d848980-e296-11eb-951e-865bfa8702b7.png)

이후 설치가 완료됩니다. (참 쉽다!)

#  samba 환경 설정 

'/etc/samba/smb.conf' 파일을 열어 환경설정을 해줍니다. 

```
pi@raspberrypi:~ $ sudo vi /etc/samba/smb.conf
```

파일의 하단에 옵션을 작성해줍니다. 
![image](https://user-images.githubusercontent.com/35713051/125196829-44ab9780-e296-11eb-881d-05cde1dd9a52.png)

각 옵션에 대한 설명은 다음과 같습니다. 

* read only : 공유 디렉토리를 읽기만 가능하다
* writeble users : 공유 디렉토리를 쓰기 가능하다. 
* valid users : 공유 디렉토리에 로그인 할 수 있도록 한다.
* public, guest ok : 다른 사용자들이 이용할 수 있도록 한다. 
* browseable : 공유 디렉토리의 리스트를 보여준다.
* printable : 공유 디렉토리에 스풀 파일을 저장할 것인지를 지정한다.
* path : 공유할 디렉토리의 절대 경로이다.
* comment : 코멘트를 뜻한다.
* create mask, creat mode : 파일을 생성할 때 사용되는 모드를 의미한다.
* write list : 쓰기가 가능한 특정 사용자를 지정한다.

conf 파일을 작성하였다면 저장 후 종료해줍니다. 

# samba 계정 설정

삼바 사용을 위해서 사용자를 설정해주어야 합니다. 
'smbpasswd' 를 이용하여 사용자를 추가해줍니다. 

![image](https://user-images.githubusercontent.com/35713051/125196833-4aa17880-e296-11eb-9c19-66cf038c2e01.png)

저는 pi 계정을 추가하였습니다. 
패스워드를 입력하라는 메시지가 나타나면 사용하고자 하는 패스워드를 입력해주시면 됩니다. 

입력 후에는 smb 데몬을 재기동해줍니다. 

# 윈도우에서 samba 접속 

'[윈도우] + r' 을 입력하여 실행창을 띄웁니다. 
이후 '\\[라즈베리파이IP]' 를 입력해줍니다. 

![image](https://user-images.githubusercontent.com/35713051/125196840-512ff000-e296-11eb-9e22-921e6072c157.png)

아이피를 맞게 입력하였다면 아래와 같이 연결이 됩니다. 
![image](https://user-images.githubusercontent.com/35713051/125196845-55f4a400-e296-11eb-9136-1f5c7fc7a9a0.png)

계정명으로 된 경로를 더블클릭하면 자격증명 화면이 나타납니다. 
이전에 환경설정때 입력한 계정명과 passwd를 입력해줍니다. 
![image](https://user-images.githubusercontent.com/35713051/125196858-69a00a80-e296-11eb-9c2e-56016f576ff9.png)

정상적으로 접근이 되었습니다! 

![image](https://user-images.githubusercontent.com/35713051/125196863-70c71880-e296-11eb-9e17-6d5dce19cc85.png)



