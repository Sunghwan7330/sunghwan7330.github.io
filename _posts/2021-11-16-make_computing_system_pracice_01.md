---
title: "[밑바닥부터 만드는 컴퓨팅] 프로젝트01 논리게이트 제작"
excerpt: "해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템'  프로젝트01 논리게이트 부분을 정리하였습니다. "

categories:
  - 'make_computing_system'
tags:
  - 밑바닥부터 만드는 컴퓨팅

toc: true
toc_stic표ky: true

date: 2021-11-06
last_modified_at: 2021-11-16
---

# 개요 

해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 프로젝트 01 의 내용을 정리하려 합니다. 
혹시나 따라하실 분은 실습준비 포스팅을 참고해서 프로그램을 다운받아주시면 됩니다. 

# 프로그램 실행 

다운받은 프로그램에서 `HardwareSimulator.bat` 를 실행해줍니다. 

# 게이트 제작 

## Not 게이트 제작

처음에는 Nand 게이트만 지원합니다. 
Nand게이트의 진리표는 아래와 같습니다. (And의 반대입니다)

| a | b | out |
| --- | --- | --- |
| 0 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

우리는 이 게이트를 시작으로 하여 모든 게이트를 제작하는 것을 목표로 합니다. 


가장 먼저 Not 게이트를 만들어보려 합니다. 
Not 게이트의 진리표는 아래와 같습니다. 

| in | out | 
| --- | --- |
| 0 | 1 | 
| 1 | 0 |

Not은 입력된 비트를 반전시켜줍니다. 
Nand로 Not를 어떻게 구성할지 생각해보았을 때 Nand의 진리표를 잘 보면 a와 b에 같은 값을 입력하였을 때 반대되는 값이 나오는 것을 보실 수 있습니다. 

이제 이것을 HDL로 작성해주면 됩니다. 
저도 이번에 HDL이라는 언어를 처음 접해보았는데, 자세히는 모르지만 게이트를 구현할 수준으로는 작성하는데 어렵지 않습니다. 

우선 `projects\01` 경로로 이동합니다. 
해당 경로에 많은 파일들이 있습니다. 
확장자별로 다음과 같은 역할을 합니다. 

* hdl : 우리가 작성해서 할 파일
* tst : 작성한 hdl을 테스트하기 위한 스크립트 
* cmp : 출력된 결과를 비교하기 위한 파일 

우선 텍스트편집기를 통해 not.hdl 파일을 열어줍니다. 

```
CHIP Not {
    IN in;
    OUT out;

    PARTS:
    // Put your code here:
}
```

위와 같은 부분이 있는데, PARTS:  아래의 부분에 코드를 작성하야 합니다. 
현재 사용할 수 있는 칩은 Nand 뿐입니다. 
위에서 설명한대로 hdl 코드를 작성해보도록 하겠습니다. 

Nand는 a와 b를 입력받고 out으로 출력합니다. 
우리가 구현하려는 Not 은 in으로 입력받고 out 으로 출력합니다. 

hdl 코드를 아래와 같이 작성해줍니다.
```
CHIP Not {
    IN in;
    OUT out;

    PARTS:
    // Put your code here:
    Nand(a=in, b=in, out=out);
}
```

위의 코드는 `in` 변수를 입력받고 `out` 변수에 출력값을 써야합니다. 

작성된 Nand 부분의 코드는 다음과 같습니다 .
Nand의 입력인 `a`와 `b`에는 `in`을 입력하고, 출력인 `out`에는 `out`을 입력해줍니다. 
마지막의 `out=out` 이조금 햇갈릴 수 있는데, `=`을 기준으로 왼쪽의`out`은 Nand에서 출력하기 위해 입력받는 부분이고, 
오른쪽의 `out`은 출력된 값을 저장하는 변수입니다. 

이제 제작한 게이트를 프로그램을 통해 로드한 후 테스트를 해보도록 합시다.

## And 게이트 제작

Not게이트를 제작하였으니 이제 And 게이트를 만들어보겠습니다. 
And게이트는 Nand게이트에 출력값을 Not으로 반전시켜주면 되겠습니다. 

```
CHIP And {
    IN a, b;
    OUT out;

    PARTS:
    // Put your code here:
    Nand(a=a, b=b, out=c);
    Not(in=c, out=out);
}
```

먼저 Nand에 `a`와 `b`를 입력하고 출력을 `c`에 저장합니다. 
이후 Not게이뭉트에 `c`를 입력하고 `out`에 출력하면 And 게이트가 완성됩니다. 

이와 같은 방식으로 모든 게이트를 완성해나가면 됩니다. 

## And16 게이트 제작 

hdl 작성 및 프로그램 구동법에 대해서는 위에서 설명하였으니, 해당 절 부터는 간단한 설명 후 완성된 코드만 공개하겠습니다. 
 
And16은 16비트의 값 `a`와 `b`를 and 연산하여 출력하는 게이트입니다. 
hdl에서 배열의 값들 입력해주어야하는데, 코드를 보면 어렵지 않습니다. 

```
CHIP And16 {
    IN a[16], b[16];
    OUT out[16];

    PARTS:
    // Put your code here:
    And(a=a[0], b=b[0], out=out[0]);
    And(a=a[1], b=b[1], out=out[1]);
    And(a=a[2], b=b[2], out=out[2]);
    And(a=a[3], b=b[3], out=out[3]);
    And(a=a[4], b=b[4], out=out[4]);
    And(a=a[5], b=b[5], out=out[5]);
    And(a=a[6], b=b[6], out=out[6]);
    And(a=a[7], b=b[7], out=out[7]);
    And(a=a[8], b=b[8], out=out[8]);
    And(a=a[9], b=b[9], out=out[9]);
    And(a=a[10], b=b[10], out=out[10]);
    And(a=a[11], b=b[11], out=out[11]);
    And(a=a[12], b=b[12], out=out[12]);
    And(a=a[13], b=b[13], out=out[13]);
    And(a=a[14], b=b[14], out=out[14]);
    And(a=a[15], b=b[15], out=out[15]);    
}
```
