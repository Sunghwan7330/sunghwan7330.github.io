---
title: "[밑바닥부터 만드는 컴퓨팅] 프로젝트02 산술논리연산장치 제작"
excerpt: "해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템'  프로젝트02  부분을 정리하였습니다. "

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

해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 프로젝트 02 의 내용을 정리하려 합니다. 
혹시나 따라하실 분은 실습준비 포스팅을 참고해서 프로그램을 다운받아주시면 됩니다. 

# 프로그램 실행 

다운받은 프로그램에서 `HardwareSimulator.bat` 를 실행해줍니다. 

![image](https://user-images.githubusercontent.com/35713051/142761388-9d8c0dc9-0cfb-4c4e-bdde-8b2fc20c87d3.png)

UI에서 보이는 화면 중 각 표시한 버튼은 아래와 같습니다. 

* ① : hdl 파일을 로드합니다. 
* ② : 스크립트를 실행합니다. 
* ③ : 스크립트를 로드합니다. 

# 반가산기 제작

반가산기는 두 비트 a와 b를 입력받아 더하여 `sum`과 `carry`를 계산하는 연산장치입니다. 

`sum`은 Xor 게이트로 구현이 가능합니다. 
Xor게이트처럼 `sum` 도 두 비트의 값이 같을때 0, 아닐때 1이 됩니다. 

`carry`는 And 게이트로 구현이 가능합니다. 
두 비트가 모두 1일때만 carry가 발생하기 때문입니다. 

따라서 아래와 같이 구현이 가능합니다.

```
CHIP HalfAdder {
    IN a, b;    // 1-bit inputs
    OUT sum,    // Right bit of a + b 
        carry;  // Left bit of a + b

    PARTS:
    // Put you code here:
    Xor(a=a, b=b, out=sum);
    And(a=a, b=b, out=carry);
}

```
