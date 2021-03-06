---
title: "[밑바닥부터 만드는 컴퓨팅] 12장 운영체제"
excerpt: "해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 12장 내용을 정리하였습니다. "

categories:
  - 'make_computing_system'
tags:
  - 밑바닥부터 만드는 컴퓨팅
  - 운영체제

toc: true
toc_sticky: true

date: 2022-04-15
last_modified_at: 2022-04-15
---

# 1. 배경 

## 1.1 수학 연산 

컴퓨터 시스템은 덧셈, 곱셈, 나눗셈같은 수학 연산을 지원합니다. 
덧셈의 경우 ALU 수준에서 구현되며, 이는 3장에서 구현했었습니다. 
곱셈 및 나눗셈같은 연산들은 하드웨어나 소프트웨어에서 처리되며, 비용 / 성능 요구사항에 따라 구현 방식이 달라집니다. 
해당 절에서는 곱셈, 나눗셈 및 제곱근 연산을 OS 수준에서 구현합니다. 

### 1.1.1 효율성 우선 

수학 알고리즘은 n비트 2진수로 연산되며, 전형적인 컴퓨터 아키텍쳐에서는 32비트 또는 64비트를 사용합니다. 

예를 들어 `x * y` 를 for문을 이용해 덧셈으로 구현한다고 해보겠습니다. 
이때 y의 값이 최대값이라면 현존하는 가장 빠른 컴퓨터라고 해도 몇년이 걸릴수도 있습니다. 
반면 곱셈 알고리즘을 이용하여 수행한다면 빠르게 수행이 가능합니다. 

보통 알고리즘의 실행시간을 표기할때 보통 ‘빅 오’ 표기법인 O(n)을 사용합니다. 

### 1.1.2 곱셈

![image](https://user-images.githubusercontent.com/35713051/166141460-be9d1fb7-5163-481c-b164-465dce105890.png)

위 그림의 알고리즘은 n 비트 숫자에 O(n) 덧셈 연산을 수행합니다. 
비트 표현을 왼쪽으로 한 칸 이동시키거나 shiftedX 를 자기 자신에 더하면 shifted * 2 를 효율적으로 얻을 수 있습니다. 
두 연산 모두 ALU 연산으로 쉽게 수행할 수 있습니다. 

### 1.1.3 나눗셈 

```
divide (x, y):
  // x>=0이고 y > 0일 때 x/y의 정수부
  if y>x return 0
    q = divide(x, 2-y)
  if (x - 2 * q * y) < y
    return 2 * q
  else
    return 2 * q + 1
```

이 알고리즘의 실행 시간은 재귀의 깊이에 좌우됩니다. 
재귀의 각 단계에서 y의 값에 2가 곱해지고, y>x이면 중단하므로 재귀의 깊이는 x의 비트수인 n으로 제한됩니다. 
그리고 재귀의 단계마다 상수 횟수만큼 덧셈, 뺄셈 및 곱셈 연산이 수행되므로 전체 실행시간은 O(n)이 됩니다. 

### 1.1.4 제곱근 

제곱근은 뉴턴 랩슨(newton-Raphson) 방법이나 테일러 급수 전개(Taylor series expansion) 과 같은 효율적인 계산 방법이 있습니다.
하지만 여기서는 더 간단한 방법으로 충분합니다. 
제곱근 함수 `y = √(x)` 에는 다음과 같은 속성이 있습니다. 

* 단조 증가함수
* 역함수 `x = y^2` 를 계산하는 방법을 알고 있음 

```
sqrt(x):
  // y =vx의 정수부를 계산한다. 전략:
  // y≤x< (y+1)인 정수 y를 찾는다. (0≤x<2일 때)
  // 0...22-1의 범위에서 2진 탐색을 수행한다.
  y = 0
  for j = n/2 - 1 … 0 do
    …..
  return y
```

## 1.2 숫자의 문자열 표현

컴퓨터는 숫자를 내부적으로는 2진 코드로 표현합니다. 
OS는 특정 2진 코드를 문자로 변경하여 제공하는 루틴이 포함되있습니다.
간단한 문자들은 ASCII 코드로 정리되어 있습니다. 

문자 ‘0’ 은 0x30이며 문자’9’는 0x39 입니다. 
아래의 예시는 문자를 숫자로, 숫자를 문자로 변환하는 방법입니다. 

```
// 음수가 아닌 숫자를 문자열로 변환한다.
int2String(n):
  lastDigit=n96 10
  c = lastDigit을 나타내는 문자
  if n < 10
    return c (문자열 타입으로 반환)
  else
    return int2String(n/10).append(c)
```

```
// 문자열을 숫자로 변환한다.
string2Int(s);
  v = 0
  for i=1 ... (s의 length) do
    d = s[i]의 int 값
    v = v * 10 + d
  return v
  // s[1]이 s의 가장 윗자리 숫자를
  // 나타내는 문자라 가정한다.
```

## 1.3 메모리 관리 

### 1.3.1 동적 메모리 할당 

고수준 언어의 장점 중 하나는 프로그래머가 변수들에 RAM을 할당하고, 그 변수가 필요없을 때 메모리 공간을 재활용하는 세부 작업에 대해서 신경쓰지 않아도 된다는 점입니다. 
모든 메모리 관리 작업들은 컴파일러, 운영체제, 가상머신이 처리해줍니다. 

서로 다른 변수들은 프로그램의 생명 주기 내에서 서로 다른 시점에 메모리를 할당받습니다. 
예로 정적 변수는 컴파일 시간에 컴파일러가 할당하는 반면, 지역변수는 서브루틴이 실행될 때 마다 스택상에 할당됩니다. 
그 외의 메모리는 프로그램 실행 중 동적으로 할당되며, 이때 OS가 개입합니다. 
C 의 malloc 함수나 C++, 자바의 new 인스턴스를 사용할때마다 메모리 블록이 할당됩니다. 
이후 더이상 쓸모없어졌을 때는 RAM 공간이 재활용됩니다. 
C, C++과 같은 언어는 메모리 공간 해제를 프로그래머가 직접 하지만, 자바와 같은 언어에서는 가비지컬렉션이 자동으로 처리합니다. 

### 1.3.2 기초 메모리 할당 알고리즘 

이 알고리즘이 다루는 데이터 구조는 단일 포인터로, free라 불리며 아직 할당되지 않은 힙 시작부분을 가리킵니다. 

```

Initialization: free = heapBase
// size개의 단어들에 해당하는 메모리 블록을 할당한다.
alloc(size):
  pointer = free
  free = free + size
  return pointer

// 주어진 object의 메모리 공간을 해제한다.
deAlloc(object):
  do nothing
```

이 알고리즘의 경우 더이상 사용하지 않는 객체 공간을 회수하지 않아 공간낭비가 심합니다. 

### 1.3.3 향상된 메모리 할당 알고리즘 

이 알고리즘은 사용 가능한 메모리 세그먼트들의 연결 리스트인 freeList를 관리합니다. 
각 세그먼트에는 두 가지 관리용 필드가 있습니다. 
* 세그먼트의 길이 
* 리스트에서 다음 세그먼트를 가리키는 포인터

![image](https://user-images.githubusercontent.com/35713051/166141478-dd32cc9e-9121-4f7d-9fb9-fd606fe1070d.png)

```

Initialization:
    freeList=heapBase
    freeList length=heapLength
    freelist. next=null

// size 개수의 단어만큼 메모리 공간을 할당한다.
alloc(size):
    최적적합이나 최초적합 발견법으로 freeList를 탐색해서
      segment.length > size인 세그먼트를 구한다.
    만약 그런 세그먼트를 못 찾으면, 실패값을 반환한다
      (또는 조각 모음을 시도한다).
    block=찾은 세그먼트에서 필요한 부분
      (또는 세그먼트에 남은 부분이 너무 작다면 전부)
    이 할당을 반영하도록 freeList를 업데이트한다.
    block-1]=size+1 // 메모리 해제를 위해 블록 크기를 기억한다.
    Retum block

// 객체를 해제한다.
dealloc(object):
    segment=object-1
    segment:length=object(-1)
    segment를 freeList에 삽입한다.
```

어떤 크기의 메모리 블록 할당을 요청 받으면 freeList 에서 적당한 세그먼트를 찾아야 합니다. 
적당한 세그먼트가 발견되면, 그 세그먼트에서 요청된 메모리 블록을 가져옵니다. 
다음으로 이 세그먼트에서 할당 후 나머지 부분이 freeList에 업데이트됩니다. 
블록에 메모리가 없거나 나머지 부분이 실제로 너무 작다면 전체 세그먼트가 freeList에서 제거됩니다. 

이와 같은 알고리즘은 시간이 지나면 블록 파편화 문제를 일으킬 수 있습니다. 
따라서 분리된 세그먼트를 합쳐주는 일종의 조각모음 연산을 고려해야 합니다. 


## 1.4 가변 길이 배열과 문자열 

고수준 언어에서는 String 클래스와 같은 문자열 객체를 사용하여 간편하게 문자열을 사용합니다. 
이 문자열 객체는 실제로는 배열로 구현이 가능합니다. 
일반적으로 문자열이 생성되면 가능한 긴 길이의 배열이 할당됩니다. 
그리고 문자열의 실제 길이는 이 최대길이보다 짧으며, 문자열 생애주기동안 그 길이가 저장됩니다. 
보통 현재 문자열 길이를 넘어서는 배열 위치는 문자열에 포함되지 않는다고 간주합니다. 

대부분의 프로그래밍 언어는 문자열타입 외에도 가변 길이 데이터타입을 지원합니다. 
자바의 StringBuffer, C의 strXXX 함수들이 그 예시입니다. 
