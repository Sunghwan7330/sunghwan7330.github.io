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

값의 read 는 DFF의out을 받아오면 되고, 값의 저장은 in에 값 입력후 load를 1로 주면 되겠습니다. 

# 16bit 레지스터 제작 

```
CHIP Register {
    IN in[16], load;
    OUT out[16];

    PARTS:
    Bit(in=in[0], load=load, out=out[0]);
    Bit(in=in[1], load=load, out=out[1]);
    Bit(in=in[2], load=load, out=out[2]);
    Bit(in=in[3], load=load, out=out[3]);
    Bit(in=in[4], load=load, out=out[4]);
    Bit(in=in[5], load=load, out=out[5]);
    Bit(in=in[6], load=load, out=out[6]);
    Bit(in=in[7], load=load, out=out[7]);
    Bit(in=in[8], load=load, out=out[8]);
    Bit(in=in[9], load=load, out=out[9]);
    Bit(in=in[10], load=load, out=out[10]);
    Bit(in=in[11], load=load, out=out[11]);
    Bit(in=in[12], load=load, out=out[12]);
    Bit(in=in[13], load=load, out=out[13]);
    Bit(in=in[14], load=load, out=out[14]);
    Bit(in=in[15], load=load, out=out[15]);
}
```

위에서 Bit를 제작하였습니다. 
이를 16개 연결하면 16비트의 레지스터를 만들 수 있습니다. 
레지스터는 bit와 같지만 값을 16비트 단위로 저장할 수 있다는 차이만 있습니다. 

# PC 제작 

```
CHIP PC {
    IN in[16],load,inc,reset;
    OUT out[16];

    PARTS:
    Inc16(in=regOutput, out=regOutputInc);    
    Mux16(a=regOutput, b=regOutputInc, sel=inc, out=out1);
    Mux16(a=out1, b=in, sel=load, out=out2);
    Mux16(a=out2, b=false, sel=reset, out=regInput);
    
    // load == load OR inc OR reset !!!
    Or(a=load, b=inc, out=loadOrInc);
    Or(a=loadOrInc, b=reset, out=loadOrIncOrReset);
    
    Register(in=regInput, load=loadOrIncOrReset, out=regOutput);    
    
    Or16(a=false, b=regOutput, out=out); // dummy OR gate for output
}
```

HDL 이 조금 복잡하여 그림을 직접 그려보았습니다. 

![image](https://user-images.githubusercontent.com/35713051/148063685-92eb0bcc-eea5-49fb-908e-20cb3cf57698.png)

어떻게 보이실지는 모르겠지만, 저는 이정도 되니 그나마 볼만했습니다. 
입력되는 신호는 색상으로 표현하였습니다. 

* in :  붉은색 
* inc : 파란색
* load : 초록색
* reset : 분홍색 

PC는 값을 1 증가시키는 것이 기본 동작이며, 그 외에추가적으로  특정 값으로 이동하거나 카운트를 초기화하는 기능이 있습니다. 

hdl로 제작한 PC에서는 inc, load, reset 의 신호를 입력하여 PC를 제어합니다. 

* inc : PC의 값을 1 증가
* load : 카운트를 in에 입력된 값으로 변경
* reset : 카운트를 0으로 변경 

그럼 위에 첨부된 그림을 기준으로 설명드리겠습니다. 

가장먼저 왼쪽의 `Inc16` 을 보시면 맨 오른쪽에 `Register` 에서 출력된 값이 나와 값을 1 증가시켜줍니다. 

이후 첫번째 `Mux16` 에서 `a`로 입력되는 값은 `Register`에서 출력된 값이고, `b`에 입력되는 값은 `Inc16`에서 입력된 값입니다.  `inc`로 입력된 신호가 `Mux16`의 `sel`로 입력하여 어떠한 값을 `out`으로 출력할지 결정합니다. `inc`가 1로 입력된다면 `a`에 입력된 1이 증가된 값이 출력되게 됩니다. 

그다음에 보이는 `Mux16` 에서 `a`로 입력되는 값은 이전의 `Mux16`에서 출력된 값이고, `b`에 입력되는 값은 `in`으로 입력된 값이며, `sel`에 입력되는 값은 `load`로 입력된 값입니다. `load`가 1이라면 `in` 의 값이 선택되고, 아니라면 이전의 값이 선택되게 됩니다. 

이후 보이는 `Mux16` 에서 `a`로 입력되는 값은 이전의 `Mux16`에서 출력된 값이고, `b`에 입력되는 값은 `false`이며, `sel`에 입력되는 값은 `reset`로 입력된 값입니다. `reset`가 1이라면 `false` 가 입력되어 카운트가 0이 되며, 아니라면 이전의 값이 선택되게 됩니다. 

여기까지 진행되었을 때 가장 마지막 `Mux16` 에서 나온 값을 `Register` 에 입력합니다. 
`Register` 의 load 값은 PC에 입력된 flags에 의해 결정되게 됩니다. 
`OR`게이트를 이용하여  `inc`, `load`, `reset` 셋 중 하나가 1이라면 1이 되도록 하여 `Register`의 `load` 에 입력될 수 있도록 합니다. 
만약 `inc`, `load`, `reset` 이 모두 0으로 입력되었다면 `Register`의 값은 변하지 않게 됩니다. 

위와 같이 살펴보았을 때 PC는 `inc`, `load`, `reset` 의 값에 의해 카운트를 변화하는 역할을 한다는 것을 알 수 있었습니다. 
요약하자면 PC는 `Register`의 값과, 1 추가된 값, `in` 에 입력된 주소값, 0 이 네개의 값을 멀티플렉서를 이용하여 선택할 수 있도록 한 뒤 결과값을 `Register` 에 입력해주도록 구성해주면 되겠습니다. 

# RAM8 제작


```
CHIP RAM8 {
    IN in[16], load, address[3];
    OUT out[16];

    PARTS:
    DMux8Way(in=load, sel=address, a=sel0, b=sel1, c=sel2, d=sel3, e=sel4, f=sel5, g=sel6, h=sel7);
    Register(in=in, load=sel0, out=r0);
    Register(in=in, load=sel1, out=r1);
    Register(in=in, load=sel2, out=r2);
    Register(in=in, load=sel3, out=r3);
    Register(in=in, load=sel4, out=r4);
    Register(in=in, load=sel5, out=r5);
    Register(in=in, load=sel6, out=r6);
    Register(in=in, load=sel7, out=r7);
    Mux8Way16(a=r0, b=r1, c=r2, d=r3, e=r4, f=r5, g=r6, h=r7, sel=address, out=out);
}
```

DMux 는 in에 입력된 값을 sel을 보고 해당 위치로 출력하는 게이트입니다. 
sel이 1이면 in에 입력된 값을 a에 출력하고, sel이 7이라면 in에 입력된 값을 h에 출력합니다. 

우선 모든 레지스터에 in의 값을 입력합니다.
이후 Dmux에 load 값을 입력하여 특정 Register에만 load 값이 전달되도록 합니다. 
그렇게 되면 여러 레지스터 중 하나의 레지스터에만 load 값이 전달되어 in 값이 입력됩니다. 

이후 모든 레지스터에서 출력된 값을 Mux를 이용하여 출력합니다. 
Mux의 sel에 address를 입력하여 원하는 레지스터의 값만 받아옵니다. 

위와 같이 하였을 때 특정 주소의 레지스터에 값을 입력하거나 출력할 수 있게 됩니다. 

# RAM 64 제작

```
CHIP RAM64 {
    IN in[16], load, address[6];
    OUT out[16];

    PARTS:
    DMux8Way(in=load, sel=address[3..5],a=sel0,b=sel1,c=sel2,d=sel3,e=sel4,f=sel5,g=sel6,h=sel7);
    RAM8(in=in, load=sel0, address=address[0..2], out=r0);
    RAM8(in=in, load=sel1, address=address[0..2], out=r1);
    RAM8(in=in, load=sel2, address=address[0..2], out=r2);
    RAM8(in=in, load=sel3, address=address[0..2], out=r3);
    RAM8(in=in, load=sel4, address=address[0..2], out=r4);
    RAM8(in=in, load=sel5, address=address[0..2], out=r5);
    RAM8(in=in, load=sel6, address=address[0..2], out=r6);
    RAM8(in=in, load=sel7, address=address[0..2], out=r7);
    Mux8Way16(a=r0, b=r1, c=r2, d=r3, e=r4, f=r5, g=r6, h=r7, sel=address[3..5], out=out);
}
```

RAM 64는 RAM8을 8개 연결하여 제작합니다. 
address 가 6비트이기 때문에 주소는 0 ~ 63까지 입력이 가능합니다. 

앞에서 제작한 RAM8은 3비트의 arrress인  0~7까지 입력이 가능합니다. 
따라서 DMux8Way 에 주소값 3비트를 입력하여 어느 RAM8에 입력할 지를 결정합니다. 
이후 나머지 주소의 3비트를 RAM8에 입력하여 올바른 주소의 레지스터에 작성될 수 있도록 합니다. 
마지막으로 레지스터로부터 나온 RAM8의 출력을 Mux8Way16 에 의해 분류되어 출력될 수 있도록 합니다. 

# 참고 

* https://github.com/simulacre7/nand2tetris
