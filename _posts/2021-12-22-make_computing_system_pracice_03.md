---
title: "[밑바닥부터 만드는 컴퓨팅] 프로젝트03 순차논리장치 제작"
excerpt: "해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템'  프로젝트03  부분을 정리하였습니다. "

categories:
  - 'make_computing_system'
tags:
  - 밑바닥부터 만드는 컴퓨팅

toc: true
toc_stic표ky: true

date: 2021-12-22
last_modified_at: 2021-12-22
---

# 개요 

해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 프로젝트 03 의 내용을 정리하려 합니다. 
혹시나 따라하실 분은 실습준비 포스팅을 참고해서 프로그램을 다운받아주시면 됩니다. 

# 프로그램 실행 

다운받은 프로그램에서 `HardwareSimulator.bat` 를 실행해줍니다. 

![image](https://user-images.githubusercontent.com/35713051/142761388-9d8c0dc9-0cfb-4c4e-bdde-8b2fc20c87d3.png)

UI에서 보이는 화면 중 각 표시한 버튼은 아래와 같습니다. 

* ① : hdl 파일을 로드합니다. 
* ② : 스크립트를 실행합니다. 
* ③ : 스크립트를 로드합니다. 

# Bit 제작 

```
CHIP Bit {
    IN in, load;
    OUT out;

    PARTS:
    Mux(a=dffOut, b=in, sel=load, out=muxOut);
    DFF(in=muxOut, out=dffOut);
    Or(a=false, b=dffOut, out=out); /*A limitation of HDL*/
}
```

![image](https://user-images.githubusercontent.com/35713051/147379460-0b8d4f87-6c4e-425c-bd83-840217fe5a91.png)

bit는 1비트 레지스터입니다. 
이전 포스팅에서 설명한바와 같이 Mux와 DFF를 이용하였습니다. 
Bit는 시간이 지나도 1비트의 값을 저장할 수 있는 기본적인 저장장치입니다. 

load가 1이라면 Mux에서 입력된 값인 in이 출력됩니다. 
이후 출력된 값이 DFF에 입력됩니다. 
다음 클럭에서 load가 0이라면 이전에 입력된 값이 Mux 로 입력되어 다시 출력됩니다. 
그렇기 때문에 load가 1이 입력되기 전까지는 이전에 입력된 값이 계속 유지되게 됩니다. 
(OR은 무시하셔도 됩니다.)
