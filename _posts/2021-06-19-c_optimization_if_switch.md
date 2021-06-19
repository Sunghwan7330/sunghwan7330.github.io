---
title: "[C코드 최적화] if가 좋을까 Switch가 좋을까?"
excerpt: "해당 내용은 '임베디드 프로그래밍 C코드 최적화' 책의 story 16에 내용을 정리하였습니다. "

categories:
  - 'embedded_c_optimization'
tags:
  - c코드 최적화
  - 북스터디
  - 최적화
  - 분기 최적화
  - if문
  - switch문

toc: true
toc_sticky: true

date: 2021-06-19
last_modified_at: 2021-06-19
---

# 개요 

해당 내용은 '임베디드 프로그래밍 C코드 최적화' 책의 story 16에 내용을 정리하였습니다.

분기문 처리시 if문과 switch 문을 효과적으로 사용하는 방법에 대해 알아보려 합니다. 

# 분기문 최적화

test1, test2, test3 함수는 같은 동작을 하는 함수입니다. 
어떤 차이가 있는지 한번 봐주시기 바랍니다.

```c
#include <stdio.h>

void test1() {
  int a, b, c, d, x = 0;
  a = b = 3;
  c = 5;
  d = 6;
  if(((a*c)+b)/d == 0)
    x = ((a*c)+b)/d + 5;
  else if (((a*c)+b)/d == 1)
    x = ((a*c)+b)/d + 10;
  else if (((a*c)+b)/d == 2)
    x = ((a*c)+b)/d + 15;
  else if (((a*c)+b)/d == 3)
    x = ((a*c) +b)/d + 20;
  else if (((a*c)+b)/d == 4)
    x = ((a*c)+b)/d + 25;
  else
    x = 0;
  printf("%d\n", x);
}

void test2() {
  int a, b, c, d, x = 0, t;
  a = b = 3;
  c = 5;
  d = 6;
  t = ((a*c)+b)/d;

  if(t == 0)
    x = t + 5;
  else if (t == 1)
    x = t + 10;
  else if (t == 2)
    x = t + 15;
  else if (t == 3)
    x = t + 20;
  else if (t == 4)
    x = t + 25;
  else
    x = 0;
  printf("%d\n", x);
}

void test3() {
  int a, b, c, d, x = 0, t;
  a = b = 3;
  c = 5;
  d = 6;
  t = ((a*c)+b)/d;
  
  switch(t) {
    case 0:
	  x = t + 5;
	  break;
    case 1:
	  x = t + 10;
	  break;
    case 2:
	  x = t + 15;
	  break;
    case 3:
	  x = t + 20;
	  break;
    case 4:
	  x = t + 25;
	  break;
    default:
	  x = 0;
	  break;
  }
  printf("%d\n", x);
}

void main() {
  test1();
  test2();
  test3();
}
```

test1, test2, test3 함수는 같은 결과를 보이지만 로직을 보았을때는 차이가 있습니다. 

먼저 test1과 test2 의 가장 큰 차이는 t 의 유무입니다. 
test1의 경우 t 변수를 사용하지 않았습니다. 
그렇기 때문에 모든 if 문에서 `((a*c)+b)/d` 를 계속 계산하고 있습니다. 
항상 같은 값이 나오는 연산을 매번 반복하는 로직은 좋지 않습니다.
test2 와 같이 계산된 값을 변수에 저장하고 사용하는 편이 좋겠습니다. 

그럼 test2와 test3의 차이를 알아보겠습니다. 
분기문에서 test2 함수는 if를 사용하였고, test3 은 switch를 사용하였습니다. 
코드를 보았을 때는 많은 차이가 보이지 않습니다. 
objdump를 통해 어셈블리를 확인하였을 때 어떤 차이가 있는지 확인해봅시다. 

* test2 함수 어셈블리 일부 
```

  if(t == 0)
   106b8: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   106bc: e3530000  cmp r3, #0
   106c0: 1a000003  bne 106d4 <test2+0x78>
    x = t + 5;
   106c4: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   106c8: e2833005  add r3, r3, #5
   106cc: e50b3008  str r3, [fp, #-8]
   106d0: ea00001d  b 1074c <test2+0xf0>
  else if (t == 1)
   106d4: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   106d8: e3530001  cmp r3, #1
   106dc: 1a000003  bne 106f0 <test2+0x94>
    x = t + 10;
   106e0: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   106e4: e283300a  add r3, r3, #10
   106e8: e50b3008  str r3, [fp, #-8]
   106ec: ea000016  b 1074c <test2+0xf0>
  else if (t == 2)
   106f0: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   106f4: e3530002  cmp r3, #2
   106f8: 1a000003  bne 1070c <test2+0xb0>
    x = t + 15;
   106fc: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   10700: e283300f  add r3, r3, #15
   10704: e50b3008  str r3, [fp, #-8]
   10708: ea00000f  b 1074c <test2+0xf0>
  else if (t == 3)
   1070c: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   10710: e3530003  cmp r3, #3
   10714: 1a000003  bne 10728 <test2+0xcc>
    x = t + 20;
   10718: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   1071c: e2833014  add r3, r3, #20
   10720: e50b3008  str r3, [fp, #-8]
   10724: ea000008  b 1074c <test2+0xf0>

.....

```

* test3 함수 어셈블리 일부 
```


  switch(t) {
   107c4: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   107c8: e3530004  cmp r3, #4
   107cc: 979ff103  ldrls pc, [pc, r3, lsl #2]
   107d0: ea000018  b 10838 <test3+0xd0>
   107d4: 000107e8  .word 0x000107e8
   107d8: 000107f8  .word 0x000107f8
   107dc: 00010808  .word 0x00010808
   107e0: 00010818  .word 0x00010818
   107e4: 00010828  .word 0x00010828
    case 0:
    x = t + 5;
   107e8: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   107ec: e2833005  add r3, r3, #5
   107f0: e50b3008  str r3, [fp, #-8]
    break;
   107f4: ea000012  b 10844 <test3+0xdc>
    case 1:
    x = t + 10;
   107f8: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   107fc: e283300a  add r3, r3, #10
   10800: e50b3008  str r3, [fp, #-8]
    break;
   10804: ea00000e  b 10844 <test3+0xdc>
    case 2:
    x = t + 15;
   10808: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   1080c: e283300f  add r3, r3, #15
   10810: e50b3008  str r3, [fp, #-8]
    break;
   10814: ea00000a  b 10844 <test3+0xdc>
    case 3:
    x = t + 20;
   10818: e51b301c  ldr r3, [fp, #-28]  ; 0xffffffe4
   1081c: e2833014  add r3, r3, #20
   10820: e50b3008  str r3, [fp, #-8]
    break;
   10824: ea000006  b 10844 <test3+0xdc>

......
```

test2 함수는 if문 마다 비교문이 있는 반면 test3 함수는 switch 시작부분에만 비교문이 있습니다. 
변수 t를 읽은 뒤 4와 비교하여 4보다 크면 default 로 분기하고, 그렇지 않으면 t에 값에 따라 지정된 레이블로 분기합니다. 

switch는 한번에 비교 후 지정된 레이블로 바로 분기하지만, if문은 비교하고 거짓이면 다음 블록에서 비교하고, 거짓이면 또 다음블록에서 비교하고.... 를 반복합니다. 
따라서 위와 같은 로직이라면 if문은 효과적이지 않습니다. 

그런데....

제가 위의 코드를 테스트 하면서 재밌는 점을 발견했습니다. 

```
void test1() {
   10408: e92d4010  push  {r4, lr}
    x = ((a*c)+b)/d + 20;
  else if (((a*c)+b)/d == 4)
    x = ((a*c)+b)/d + 25;
  else
    x = 0;
  printf("%d\n", x);
   1040c: e3a01017  mov r1, #23
   10410: e59f0004  ldr r0, [pc, #4]  ; 1041c <test1+0x14>
   10414: ebffffb3  bl  102e8 <printf@plt>
}
   10418: e8bd8010  pop {r4, pc}
   1041c: 000104d4  .word 0x000104d4

00010420 <test2>:

void test2() {
   10420: e92d4010  push  {r4, lr}
    x = t + 20;
  else if (t == 4)
    x = t + 25;
  else
    x = 0;
  printf("%d\n", x);
   10424: e3a01017  mov r1, #23
   10428: e59f0004  ldr r0, [pc, #4]  ; 10434 <test2+0x14>
   1042c: ebffffad  bl  102e8 <printf@plt>
}
   10430: e8bd8010  pop {r4, pc}
   10434: 000104d4  .word 0x000104d4

00010438 <test3>:

void test3() {
   10438: e92d4010  push  {r4, lr}
    break;
    default:
    x = 0;
    break;
  }
  printf("%d\n", x);
   1043c: e3a01017  mov r1, #23
   10440: e59f0004  ldr r0, [pc, #4]  ; 1044c <test3+0x14>
   10444: ebffffa7  bl  102e8 <printf@plt>
}
   10448: e8bd8010  pop {r4, pc}
   1044c: 000104d4  .word 0x000104d4
```

위의 결과는 -O3 최적화 옵션을 주었을 때의 결과입니다.
(컴파일러 최적화 능력 대단함....)

하지만 컴파일러에 너무 의존하는것은 좋지 않으니 필요한 부분에서 최적화할 수 있는 능력을 가지도록 합시다....! 

다시 본론으로 돌아와서....

분기분에서는 if보다 switch가 속도면에서 우월하다고 하여도 모든 분기문을 switch 로 바꿀수는 없습니다. 
그렇다면 case가 많은 if 문을 효율적으로 사용하는 방법은 없을까요?

```c
void test(int a) {
  if (a == 1) {
  } elas if (a == 2) {
  } elas if (a == 3) {
  } elas if (a == 4) {
  } elas if (a == 5) {
  } elas if (a == 6) {
  } elas if (a == 7) {
  } elas if (a == 8) {
  }
}  

void test2(int a) {
  if (a <= 4) {
    if (a == 1) {
	} elas if (a == 2) {
	} elas if (a == 3) {
	} elas if (a == 4) {
	}
  } else {
    if (a == 5) {
	} elas if (a == 6) {
	} elas if (a == 7) {
	} elas if (a == 8) {
	}
  }
}

```

위 코드에서 test1과 test2 역시 같은 동작을 하는 코드입니다. 
test1 에서 a가 7, 8 과 같은 높은숫자라고 하면 앞의 모든 비교문을 수행하기 때문에 지연이 발생합니다. 
하지만 test2 처럼 분기를 한번 더 나누게 된다면 불필요한 분기를 줄일 수 있습니다.
이러한 방법을 이진 분리 (Binary breakdown)이라고 합니다. 

다음은 다중 분기에 대한 예시를 보겠습니다. 

```c

int test1(int a) {
  switch (a) {
    case 0 : return 0x3d;
	case 1 : return 0x06;
	case 2 : return 0x5a;
	case 3 : return 0x13;
	
	......
	case 200 : return 0x61;
	default  : return 0x00;
  }
}

int test2(int a) {
  int b[] = {0x3d, 0x06, 0x5a, 0x13, ..., 0x61 }
  return b[a];
}

```

test2 함수는 test1 함수에 비해 매우 간결합니다.
또한 test2 는 값 비교를 사용하지 않고, a의 값을 배열의 인젝스로 사용하여 바로 조건에 대응하는 주소값에 접근하여 값을 얻습니다. 
이러한 동작 방식은 부하 불균형 문제를 해결해주고 비교연산과 분기를 하지 않기 때문에 속도가 빠르고 코드도 훨씬 줄어 메모리 사용도 줄여줍니다. 
