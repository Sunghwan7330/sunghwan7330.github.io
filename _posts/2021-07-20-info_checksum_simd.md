---
title: "checksum 함수 구현시 SIMD 인스트럭션 사용 주의"
excerpt: "checksum 구현시 SIMD 인스트럭션 사용하여 segfault 발생하였던 이야기를 작성하였습니다."

categories:
  - 'infomation'
tags:
  - SIMD
  - checksum
  - sse

date: 2021-07-20
last_modified_at: 2021-07-23
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

많은 값의 덧셈을 한번에 수행하기 때문에 길이가 짧으면 SIMD 를 사용할 수 없습니다. 
sum 함수의 어셈블리의 양이 길어지는 이유도 이 때문입니다. 
sum 하는 값이 적으면 SIMD 인스트럭션을 사용할 수 없기 때문에 일반적인 덧셈연산을 하는 부분으로 jump하게 됩니다. 
SIMD를 사용할수도, 하지 않을수도 있기 때문에 두 가지의 방법을 모두 사용해야하기 때문에 어셈블리의 양이 길어지게 됩니다. 

# SIMD 인스트럭션 사용 

이 글을 정성스럽게 다 읽고있는분이 계실지는 모르겠지만,,, 

위의 예제 코드를 컴파일하였을 때 SIMD 인스트럭션을 사용하지 않았을 것입니다. 

SIMD 인스트럭션을 사용하기 위해서는 컴파일시 sse 옵션을 주어야 합니다. 

그 전에 현재 사용하고 있는 CPU가 sse를 지원하는지 확인해야 합니다. 

```
$ cat /proc/cpuinfo | grep sse

...

flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf eagerfpu pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 fma cx16 xtpr pdcm pcid dca sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm ida arat epb pln pts dtherm tpr_shadow vnmi flexpriority ept vpid fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid cqm xsaveopt cqm_llc cqm_occup_llc
....
```

제가 테스트한 CPU에서는 sse, sse2, sse3, sse4_1, sse4_2를 지원하고 있습니다. 

CPU에서 sse를 지원한다면 컴파일시에 sse 옵션을 주어 컴파일할 수 있습니다. 
저는 gcc 4.8.5 를 사용했으며, 아래의 옵션을 주어 컴파일하였습니다. 

`gcc -g -msse4.2 -O3 -o sse_test sse_test.c`

* -g : 디버깅을 위해 추가 (생략해도 됨)
* -msse4.2 : SIMD 인스트럭션을 사용하기위해 추가
* -O3 : 최적화 옵션, 해당 옵션을 넣어야 SIMD로 최적화를 수행함

컴파일 후 sum  함수의 어셈블리를 확인하면 코드가 길어지며 xmm 레지스터를 사용하는 SIMD 인스트럭션을 사용하는것을 확인할 수 있습니다. 


# segfault를 막기 위해서...

SIMD를 설명하느라 잠시 본 목적을 잊고있었습니다. 

컴파일러가 checksum 함수에 SIMD를 적용하는 바람에 정상적인 로직에서 segfault 가 발생하게 되었습니다. 
segfault를 막기 위해서는 어떠한 방법을 써야할까요?

성능 최적화를 위해 다른 부분에는 SIMD 를 적용해야 하지만 checksum 함수에서는 segfault 가 발생하지 않게 처리해야합니다. 

## sse 비활성화하기 

`__attribute__` 을 통해 지정한 함수만 sse를 비활성화할 수 있습니다. 
이렇게하면 SIMD를 사용하면서 원하는 함수만 sse를 비활성화 할 수 있습니다. 

```c
#if defined (__GNUC__) && (defined (__x86_64__) || defined (__i386__))
__attribute__ ((target ("no-sse")))
#endif
uint64_t sum (const uint32_t * p, size_t nwords)
{
    uint64_t res = 0;
    size_t i;
    for (i = 0; i < nwords; i++) res += p [i];
    return res;
}
```
이러면 sum 함수만 sse가 적용되지 않습니다. 

## memcpy 사용하기

memcpy를 이용하여 더할 대상을 각각 복사하여 더하는 방식입니다. 
로직이 비효율적으로 보일 수 있습니다. 이럴바에는 차라리 sse를 비활성화하는것이 더 좋아보일수도 있습니다. 
하지만 이 방법은 보이는것처럼 비효율적이지는 않습니다. 

```c
uint64_t sum2 (const char * p, size_t nwords)
{
    uint64_t res = 0;
    size_t i;
    uint32_t temp;
    for (i = 0; i < nwords; i++) {
        memcpy (&temp, p + i * sizeof (uint32_t), sizeof (temp));
        res += temp;
    }
    return res;
}
```

위의 소스를 컴파일해보면 `movdua` 대신 `movdqu` 인스트럭션을 사용하는것을 볼 수 있습니다. 
`movdqu`는 `Move Double Quadword Unaligned` 입니다. 
즉 정렬되지 않은 데이터를 `xmm` 레지스터로 move 하게됩니다. 

`movdua` 보다 `movdqu` 연산 속도가 더 느리긴 하지만 SIMD 인스트럭션을 사용할 수 있다는 장점이 있습니다. 

## 결합하여 해결하기 

```c
#if defined (__GNUC__) && (defined (__x86_64__) || defined (__i386__))
__attribute__ ((target ("no-sse")))
#endif
uint64_t sum3 (const char * p, size_t nwords)
{
    uint64_t res = 0;
    size_t i;
    uint32_t temp;
    for (i = 0; i < nwords; i++) {
        memcpy (&temp, p + i * sizeof (uint32_t), sizeof (temp));
        res += temp;
    }
    return res;
}
```

위의 코드는 gcc/intel 에서는 sse가 적용되지 않으며, 다른 아키텍쳐에서는 정상적으로 동작하게 할 수 있습니다. 


## checksum 함수 해결하기 

```c
#if defined (__GNUC__) && (defined (__x86_64__) || defined (__i386__))
__attribute__ ((target ("no-sse")))
#endif
_Bool check_ip_header_sum (const char * p, size_t size)
{
    uint32_t temp;
    uint64_t sum = 0;

    memcpy (&temp, p, 4); sum += temp;
    memcpy (&temp, p + 4, 4); sum += temp;
    memcpy (&temp, p + 8, 4); sum += temp;
    memcpy (&temp, p + 12, 4); sum += temp;
    memcpy (&temp, p + 16, 4); sum += temp;

    for (size_t i = 20; i < size; i+= 4) {
        memcpy (&temp, p + i, 4);
        sum += temp;
    }

    do {
        sum = (sum & 0xFFFF) + (sum >> 16);
    } while (sum & ~0xFFFFL);

    return sum == 0xFFFF;
}
```

`__attribute__`를 사용하여 gcc/intel 에서는 sse가 적용되지 않게 하고, 그 외의 아키텍쳐에서는 정상적으로 동작하도록 변경하였습니다. 


# 정리

checksum 이 무엇인지, segfault가 발생한 원인이 뭔지, SIMD가 무엇인지, 그리고 해결 방법에 대해서 알아보았습니다. 
간단하게 정리하려고 했지만 글을 쓰면 쓸수록 점점 길어지는 느낌이 들었고, 점점 더 대충(?) 작성한 느낌이 있긴 하지만.... 그래도 내용 이해에는 문제가 없을것으로 생각됩니다. 
더 자세한 내용은 아래의 참고페이지를 확인해주세요~!

# 참고 

* https://pzemtsov.github.io/2016/11/06/bug-story-alignment-on-x86.html
