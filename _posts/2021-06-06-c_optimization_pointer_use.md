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
last_modified_at: 2021-06-06
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


