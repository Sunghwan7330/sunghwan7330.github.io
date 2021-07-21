---
title: "checksum 함수 구현시 SIMD 인스트럭션 사용 주의"
excerpt: "checksum 구현시 SIMD 인스트럭션 사용하여 segfault 발생하였던 이야기를 작성하였습니다."

categories:
  - 'infomation'
tags:
  - SIMD
  - checksum

date: 2021-07-20
last_modified_at: 2021-07-20
---

# 개요 

직장에서 업무 수행 중 프로그램이 segfault 가 발생하였습니다. 

생성된 core 파일을 확인해보니 checksum 함수에서 segfault가 발생하였습니다. 

소스코드만 보았을때는 전혀 문제가 될만한 부분이 없었습니다. 하지만 왜 segfault 가 발생했는지 몰랐습니다. 

구글링을 해보니 거의 동일한 이슈를 정리한 글을 확인하였습니다. (하단에 참고 링크 확인)

해당 링크를 통해 어떠한 문제였는지를 확인해보겠습니다. 

# checksum 이란?

인터넷 통신을 할때는 네트워크 패킷을 주고받으며 통신을 하게 됩니다. 
간단한 예시로 웹브라우저를 통해 인터넷 페이지에 접속한다면 웹페이지 IP로 TCP 80 포트로 접속하게 됩니다. 

벌써 무슨얘기인지 모르겠다고 생각하실수 있는데, 이게 중요한 내용은 아닙니다. 
웹페이지 IP로 TCP 80 포트로 접속하기 위한 네트워크 패킷을 간단히 알아보도록 하겠습니다. 

```
[Ether header][IP Header][TCP Header][HTTP Header][contents]
```

실제 인터넷 웹페이지의 내용은 content 부분에 있습니다. 
IP 헤더에는 패킷이 어디로 가야할지가 담겨있고, TCP 헤더에는 패킷의 목적지 port와 패킷의 flag에 대한 정보가 있습니다. 
하지만 지금은 이게 중요한 부분이 아닙니다. 

checksum은 IP헤더에 포함되어 있습니다. 
checksum 은 해당 패킷이 올바른 패킷인지 불량 패킷인지 확인하기위한 값이라고 생각하시면 됩니다. 
만약 checksum 값이 올바르지 않다면 해당 패킷은 불량이라고 판단할 수 있습니다. 
패킷이 중간에 유실되었거나, 변조가 되었을 가능성이 있습니다. 

# checksum 함수 소스코드 

해당 소스는 포스트 하단의 참고 페이지에서 가져왔습니다. 

```c
_Bool check_ip_header_sum (const char * p, size_t size)
{
    const uint32_t * q = (const uint32_t *) p;
    uint64_t sum = 0;

    sum += q[0];
    sum += q[1];
    sum += q[2];
    sum += q[3];
    sum += q[4];

    for (size_t i = 5; i < size / 4; i++) {
        sum += q[i];
    }

    do {
        sum = (sum & 0xFFFF) + (sum >> 16);
    } while (sum & ~0xFFFFL);

    return sum == 0xFFFF;
}
```
checksum 의 계산은 네트워크 패킷의 덧셈으로 이뤄집니다. 
IP 헤더를 2byte 씩 나눈 뒤 4byte 변수에 전부 더합니다. 
이후 4byte 의 합을 2byte로 나누어 더하고, 한번 더 4byte 의 합을 2byte로 나누어 더합니다. 

# segfault 발생 원인

segfault가 발생한 부분은 소스코드의 for loop 안에 덧셈 부분이였습니다. 

```c
    for (size_t i = 5; i < size / 4; i++) {
        sum += q[i];
    }
```

이 부분에서 왜 segfault가 발생하였을까요?
단순히 for loop를 이용하여 데이터를 덧셈하기만 하는데 segfault가 발생하는 이유를 알수가 없었습니다. 

## 예제 코드 

그럼 아래의 예시를 보겠습니다. 

```c
#include <stdint.h>
#include <stdio.h>

extern uint64_t sum (const uint32_t * p, size_t nwords);

char x [100];

int main (void)
{
    size_t i;
    for (i = 0; i < sizeof (x); i++) x [i] = (char) i;

    for (i = 0; i < 16; i++) {
        printf ("Trying %d sum\n", (int) i);
        printf ("Done: %d\n", (int) sum ((const uint32_t*) (x + i), 16));
    }
    return 0;
}

uint64_t sum (const uint32_t * p, size_t nwords)
{
    uint64_t res = 0;
    size_t i;
    for (i = 0; i < nwords; i++) res += p [i];
    return res;
}
```

위의 소스를 구동하였을때 `main` 함수에서 i가 1일때 segfault가 발생합니다. 
아마 위의 소스를 직접 컴파일하여 구동하였을 때 segfault가 발생하지 않으실껍니다. 

근데 왜 segfault가 발생한다고 하는지.....

이는 컴파일된 인스트럭션을 확인해보아야 합니다. 

## 어셈블리 확인 

sum 함수의 어셈블리를 확인해보도록 합시다. 

```
.L13:
        movdqa   (%r8), %xmm2
        addq     $1, %rdx
        addq     $16, %r8
        cmpq     %rdx, %r9
        pmovzxdq %xmm2, %xmm1
        psrldq   $8, %xmm2
        paddq    %xmm0, %xmm1
        pmovzxdq %xmm2, %xmm0
        paddq    %xmm1, %xmm0
        ja       .L13
```

어셈블리가 길어서 메인 루프의 부분만 떼어왔습니다. 
저렇게 짧은 함수의 어셈블리가 왜 긴지는 잠시 후에 알아보겠습니다. 

아무튼 어셈블리를 보면 `movdqa` 라는 인스트럭션을 사용하고 있습니다. 
gdb를 통해 디버깅을 해보면 `movdqa` 인스트럭션에서 segfault가 발생하는것을 확인할 수 있습니다. 

## movdqa 인스트럭션

`movdqa` 는 `Move Aligned Double Quadword` 라는 인스트럭션입니다. 

말 그대로 aligned 된 double quadword(16byte) 데이터를 xmm 레지스터로 move 하는 인스트럭션입니다. 
그런데 왜 이 인스트럭션이 segfault를 발생시키는걸까요?

movdqa 인스트럭션은 aligned 된 데이터를 move 합니다. 
만약 데이터가 aligned이 맞지 않다면 segfault를 발생하게 됩니다. 
따라서 해당 인스트럭션을 사용할때는 메모리가 16byte 단위로 정렬이 되어있어야 합니다. 

앞서 보았던 예제소스에서 x+1일때 segfault가 발생한 이유는 메모리 주소의 aligned가 되지 않았기 때문입니다. 

## movdqa 을 왜 사용하는가? (SIMD)

`xmm` 레지스터는 `SIMD` 연산을 지원하는 레지스터입니다. 

`SIMD` 는 `Single Instruction Multiple Data` 의 약자로 하나의 명령어로 여러개의 값을 동시에 계산하는 방식입니다. 
주로 그래픽 작업과 같은 벡터 연산에 많이 사용됩니다. 

위의 sum 함수에서 movdqa 인스트럭션이 사용된 이유는 SIMD 연산을 수행하기 위해 xmm 레지스터로 값을 이동하는 연산을 수행한 것입니다. 

SIMD 연산을 사용하지 않았다면 loop 를 돌면서 값을 전부 더하는 작업을 수행하게 될 것입니다. 
for loop를 수행하기 때문에 루프를 한번 돌때마다 아래의 동작을 수행하게 됩니다. 

* 인덱스 `i` 증가
* `i` 의 크기 비교
* `i` 증가
* `sum` 에 값 증가 

단순한 덧셈 연산이지만 루프 한번에 꽤 많은 작업이 수행됩니다. 

하지만 SIMD 인스트럭션을 사용하면 이를 간소화할 수 있습니다. 
movdua 명령어를 통해 xmm 레지스터로 값을 이동시킨 후 paddq 로 한번에 값을 더할 수 있습니다. 
128bit(16byte) 의 값을 2바이트 단위로 더하기 때문에 for loop 를 8번 반복한 결과와 동일하게 됩니다. 



(To be continue...)

# 참고 

* https://pzemtsov.github.io/2016/11/06/bug-story-alignment-on-x86.html