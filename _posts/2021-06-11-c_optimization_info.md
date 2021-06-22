---
title: "[C코드 최적화] 최적화에 대해서..."
excerpt: "해당 내용은 '임베디드 프로그래밍 C코드 최적화' 책의 story 8에내용을 정리하였습니다."

categories:
  - 'embedded_c_optimization'
tags:
  - c코드 최적화
  - 북스터디
  - 최적화

toc: true
toc_sticky: true

date: 2021-06-11
last_modified_at: 2021-06-11
---

# 개요

해당 내용은 '임베디드 프로그래밍 C코드 최적화' 책의 story 8에내용을 정리하였습니다. 프로그램 최적화에 대하여 간단하게 소개하려 합니다. 

# 코드 최적화

요즘의 PC 완경에서는 리소스가 풍부하여 최적화가 크게 중요하게 느껴지지 않을 수 있다. 하지만.... 그런 이유로 최적화를 신경쓰지 않고 프로그램을 개발하였을 때 프로그램이 많이 느릴 수 있다. 그리고 그때 최적화를 하려고 하면 더 어려울 수 있다. 어설프게 최적화하느니 가독성을 높이는 것이 더 효과적일 수 있다. 하지만 임베디드 상황이나 특정 상황에는 최적화가 필수적일 수 있다. 

최적화의 목표는 코드 간결화가 아니다. 최적화의 목표는 상황에 따라 달라질 수 있다. 멀티미디어 시스템은 속도가 중요할 것이고, 메모리가 적은 가전제품과 같은 프로그램은 메모리 최적화가 중요할 것이다. 이처럼 최적화가 만능일 수는 없다. 무엇 하나를 얻는다면 어느쪽을 잃을 수 있다. 

가장 빠른 프로그림이 되려면 완벽한 선형이 되도록 하는것이 중요하다. 분기(branch)나 함수 호출과 같은 점프 명령을 최대한 줄이는 것이 좋다. 하지만 이를 과도하게 지키게 되면 코드가 비효율적이고 메모리 낭비가 심할수도 있다. 그래서 현 시스템에 가장 중요한 부분에 초점을 맞추어 최적화를 해야 한다. 

* 최선의 알고리즘이나 최적의 코드를 찾기
* 시스템(프로세서)를 잘 이해하기
* 개발 툴을 잘 이해하기 

하나의 예로 함수 호출시 인자를 몇개로 지정하는 것이 효과적인지 테스트하기위한 코드를 작성하였다.  비교는 ARM 프로세서와 인텔 프로세서에서 테스트를 진행하였다. 

```c
int argTest1(int a, int b, int c, int d) {
  return a+b+c+d;
}

int argTest2 (int a, int b, int c, int d, int e, int f) {
  return a+b+c+d+e+f;
}

void main() {
  int s = 0;
  s = argTest1 (1, 2, 3, 4);
  s = argTest2 (1, 2, 3, 4, 5, 6);
}
```

이후 -O3 으로 최적화하여 컴파일한 뒤 gdb를 통해 어셈블리를 확인하였다.

```
(gdb) disassemble argTest1
Dump of assembler code for function argTest1:
   0x000103d4 <+0>:     add     r0, r0, r1
   0x000103d8 <+4>:     add     r0, r0, r2
   0x000103dc <+8>:     add     r0, r0, r3
   0x000103e0 <+12>:    bx      lr
End of assembler dump.
(gdb)
(gdb) disassemble argTest2
Dump of assembler code for function argTest2:
   0x000103e4 <+0>:     add     r0, r0, r1
   0x000103e8 <+4>:     add     r0, r0, r2
   0x000103ec <+8>:     ldr     r2, [sp]
   0x000103f0 <+12>:    add     r0, r0, r3
   0x000103f4 <+16>:    ldr     r3, [sp, #4]
   0x000103f8 <+20>:    add     r0, r0, r2
   0x000103fc <+24>:    add     r0, r0, r3
   0x00010400 <+28>:    bx      lr
End of assembler dump.
(gdb)
```

ARM 프로세서의 특징 중의 하나가 레지스터가 풍부하다는 것이다. 그래서 함수의 인자를 4개까지 레지스터에 저장할 수 있다. 이것은 옵션에 의한 것이 하니라 항상 4개까지의 인자는 레지스터에 자동으로 저장되고, 인자의 개수가 4개를 넘을 때는 5번째 인자부터 스택에 저장된다. 이러한 특성 때문에 ARM 프로세서에서 함수의 성능을 향상시키려면 인자의 개수를 4개 이하로 하는 것이 좋다.


이번에는 인텔 CPU의 환경에서 컴파일한 후 어셈블리를 확인해보았다. 

```
>>> disassemble argTest1
Dump of assembler code for function argTest1:
   0x00000000004004f0 <+0>:     add    edi,esi
   0x00000000004004f2 <+2>:     add    edx,edi
   0x00000000004004f4 <+4>:     lea    eax,[rdx+rcx*1]
   0x00000000004004f7 <+7>:     ret
End of assembler dump.
>>> disassemble argTest2
Dump of assembler code for function argTest2:
   0x0000000000400500 <+0>:     add    edi,esi
   0x0000000000400502 <+2>:     add    edx,edi
   0x0000000000400504 <+4>:     add    ecx,edx
   0x0000000000400506 <+6>:     add    r8d,ecx
   0x0000000000400509 <+9>:     lea    eax,[r8+r9*1]
   0x000000000040050d <+13>:    ret
End of assembler dump.

```

어셈블리를 확인해 보면 인자 개수에 따른 변화가 없음을 알 수 있다. 
이처럼 각 프로세서의 특성에 맞는 최적화 방법이 존재하며, 이를 고려하여 최적화를 모색해야 하고, 시스템의 상황에 따라 메모리를 최적화할지 속도를 최적화할지를 결정해야 한다.
