---
title: "[C코드 최적화] 포인터의 활용과 최적화"
excerpt: "해당 내용은 '임베디드 프로그래밍 C코드 최적화' 책의 story 10에내용을 정리하였습니다."

categories:
  - 'embedded_c_optimization'
tags:
  - c코드 최적화
  - 북스터디
  - 최적화
  - 포인터

toc: true
toc_sticky: true

date: 2021-06-06
last_modified_at: 2021-06-07
---

# 개요

(포인터 관련 내용 정리중)

# 문자열 접근시 배열과 포인터의 차이

```c
#include <stdio.h>

void main(){
  char a "apple ";
  char *p = a;
  a[1] = 'k';
  p[1] = 'k';
  printf("%s\n", a);
  printf("%s\n", p);
}
```

위와 같은 테스트 코드가 있다.
'k'를 입력할 때 배열a에 입력하는것과 포인터p에 입력할 때의 결과는 동일하다. 
이때 어셈블리를 보면 아래와 같다. 

```
a[1] = 'k';
00401041 MOV byte ptr [ebp-7], 6Bh

p[1] = 'k';
00401045 mov eax, dword ptr [ebp-och)
00401048 mov byte ptr (eax+1), 6Bh
```

결과는 같지만 실제 동작에는 약간의 차이가 있다.
배열 a에 값을 입력할 때는 mov 한번으로 처리하지만, p에 값을 입력할 때는 mov를 2번 수행하게 된다. 
p에 값을 입력하기 위해서는 해당 주소값을 읽어온 뒤 값을 입력하기 때문이다. 

```
a[1] 에 접근 => acc[0x104]
p[1] 에 접근 => r0 <- [p]
                acc - [ro, #4]
```

위의 코드를 보면 a[1]에 접근할 때는 그 주소를 바로 가져온다. 
하지만 p[1]에 접근할 때는 r0 레지스터에 p의 값을 로드하고 p 값은 배열의 시작 주소이므로 주소에 4바이트를 더한 위치를 acc에 저장한다.

# 포인터는 큰 데이터를 이동시키는 효과적인 방법이다.

* 값에 의한 호출 (c 코드)

```c
#include <stdio.h>

void f1(struct Test x); 

struct Test {
  int a;
  float b;
  char C;
};

void main() {
  struct Test tl;
  tl.a = 3;
  t1.b = 5.5;
  ti.c = 'x';
  fi(t1);
}

void f1 (struct Test x) {
  printf("%d, %f, %c\n", x.a, x.b, x.c);
)

```

* 값에 의한 호출 (어셈블리)

```assembly
15: fl(t1);
0040104A sub  esp, Och
00401040 mov  eax, esp
0040104F mov  ecx, dword ptr (ebp-och)
00401052 mov  dword ptr [eax], ecx
00401054 mov  edx, dword ptr [ebp-8]
00401057 mov  dword ptr [eax+4], edx
0040105A mov  ecx, dword ptr [ebp-4]
0040105D mov  dword ptr (eax+8], ecx
00401060 call @ILT+15 (fl) (00401014)
00401065 add  esp, och
```

* 참조에 의한 호출 (c 코드)

```c
#include <stdio.h>

void f1(struct Test x); 

struct Test {
  int a;
  float b;
  char C;
};

void main() {
  struct Test tl;
  tl.a = 3;
  t1.b = 5.5;
  ti.c = 'x';
  fi(t1);
}

void f1 (struct Test *x) {
  printf("%d, %f, %c\n", x->a, x->b, x->c);
)

```

* 참조에 의한 호출 (어셈블리)

```assamble
16: fi(p);
00401050 mov  ecx, dword ptr (ebp-10h)
00401053 push ecx
00401054 call @ILT+10 (fi) (0040100f)
00401059 add  esp,4
```

위의 소스에서 중요한 부분은 f1() 함수에 값을 어떻게 전달하느냐 입니다. 
값에 의한 호출은 함수에 인자를 넘길 때 값을 복사하여 넘긴다. 
값에 의한 호출의 어셈블리를 보면 구조체의 값들을 스택에 저장하는 동작을 수행한다. 
하지만 참조에 의한 호추르이 경우 4바이트의 주소값만 복사해주면 된다. 

따라서 부피가 큰 데이터를 함수의 인자로 넘겨야 할 경우 포인터를 사용하는 것이 속도와 메모리 모두 효과적이다. 


