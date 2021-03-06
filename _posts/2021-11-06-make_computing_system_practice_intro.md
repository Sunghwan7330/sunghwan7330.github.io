---
title: "[밑바닥부터 만드는 컴퓨팅] 실습 준비"
excerpt: "해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 실습을 위한 준비 과정을 정리하였습니다. "

categories:
  - 'make_computing_system'
tags:
  - 밑바닥부터 만드는 컴퓨팅

toc: true
toc_stic표ky: true

date: 2021-11-06
last_modified_at: 2021-11-06
---

# 개요 

해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 실습 준비를 위한 과정을 정리하려 합니다. 

# 프로그램 다운로드 

아래의 웹페이지에 접속합니다. 

* https://www.nand2tetris.org/

![image](https://user-images.githubusercontent.com/35713051/140645829-d155d094-137a-4d77-a793-b07e1a39a95b.png)

이후 software를 누르면 다운로드 페이지가 나타납니다. 

![image](https://user-images.githubusercontent.com/35713051/140645850-5db62cc2-54e9-4bf0-9928-47e077ae16ca.png)

`Download the Nand2tetris Software Suite` 를 클릭하여 프로그램을 다운로드해 줍니다. 

![image](https://user-images.githubusercontent.com/35713051/140645867-999867f9-f315-449a-bed0-6d0719f2dfd1.png)

이후 압축을 해제하면 `nand2tetris` 디렉토리 안에 `project`와 `tool` 디렉토리가 있습니다. 

`project` 에는 책에서 제공하는 실습 문제가 있습니다. 
해당 문제를 해결하면서 컴퓨터를 하나하나 조립하게 됩니다. 

`tools` 에는 실제 프로그램을 실행할 수 있는 파일들이 있습니다. 
해당 프로그램은 java로 구동되기 때문에 java가 설치되지 않았다면 설치를 진행해줍니다. 

실습은 1장부터 진행하는데, 1장에서는 논리게이트를 구현하는 프로젝트가 주어집니다. 
`tools` 디렉토리에서 `HardwareSimulator.bat` 를 실행해주면 게이트를 확인할 수 있는 시뮬레이터가 실행됩니다. 
(리눅스는 제가 해보지 못했네요 ㅠㅠ)
![image](https://user-images.githubusercontent.com/35713051/140645891-78275078-bc70-4a2a-97c8-90a5ec4f82a4.png)

다음 포스팅에서는 실제 1장의 프로젝트를 수행하여 다양한 게이트를 설계해보겠습니다. 

