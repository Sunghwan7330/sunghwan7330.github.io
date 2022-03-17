---
title: "[밑바닥부터 만드는 컴퓨팅] 08장 가상 머신 2 : 프로그램 제어"
excerpt: "해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 8장 내용을 정리하였습니다. "

categories:
  - 'make_computing_system'
tags:
  - 밑바닥부터 만드는 컴퓨팅
  - 컴퓨터 아키텍쳐
  - 가상머신

toc: true
toc_sticky: true

date: 2022-03-12
last_modified_at: 2022-03-12
---

# 개요 

해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 8장 내용을 정리하였습니다.

# 1. 배경 

고수준 언어로 프로그램을 작성할 떄는 고수준의 표현을 사용할 쉬 있습니다. 
근의공식의 경우 `x=-b+sqrt(power(b, 2)-4*a*c)` 와 같이 나타낼 수 있습니다. 
고수준 언어는 다음과 같은 특징이 있습니다. 

1. sqrt나 power와 같은 고급 연산을 필요에 따라 정의할 수 있음
1. 이러한 서브루틴을 기본 연산자처럼 호출할 수 있음 
1. 서브 루틴이 실행되고 나면 코드 내에 다음 명령으로 반환됨

이러한 특징이 있기 때문에 우리는 알고리즘을 작성할 때 한층 더 가까운 추상화 코드를 작성할 수 있습니다. 
추상화 수준이 높아질수록 저수준에서 처리할 작업은 늘어납니다. 
서브루틴 호출이 일어날 때 뒷단에서는 다음과 같은 처리가 필요합니다. 

* 호출지에서 호출된 서브루틴으로 매개변수 전달 
* 호출된 서브루틴을 실행하기 전에 호출자의 상태 저장
* 호출된 서브루틴의 지역 변수에 공간 할당
* 호출된 서브루틴을 실행하기 위해 점프
* 호출된 서브루틴의 결팟값을 호출자로 반환
* 값이 반환될 때, 호출된 서브루틴이 점유한 메모리 공간을 재활용
* 호출자의 상태를 복구
* 호출자 코드에서 이전에 점프했던 지점 바로 다음 코드를 실행하기 위해 점프

이러한 작업들은 고수준 언어를 해석하는 컴퍼일러에서 해결해줍니다. 

## 1.1 프로그램 흐름 제어 

컴퓨터 프로그램은 기본적으로 한 명령씩 순차적으로 실행합니다. 
이러한 순차적 흐름은 가끔씩 분기 병령으로 끊기게 됩니다. (if, switch 등에 의한 분기)
저수준 프로그래밍에서는 분기 논리가 있을 때 goto destination 명령으로 프로그램의 특정 위치부터 실행을 지속하도록 하게 합니다. 
위치 설정의 경우 여러가지 방법이 있지만,  일반적으로는 다음에 실행할 명령어의 물리적 주소를 지정하는 것이 일반적입니다. 

다음은 if 문과 while 문의 제어 흐름 구조와 이를 의사 VM으로 변환한 코드입니다. 

* if 문 제어 흐름 구조 
```
if (cond)
  s1
else
  s2
...
```
* 의사 VM 코드 
```
  VM code for computing ~(cond)
  if-goto L1
  VM code for executing s1
  goto L2
label L1
  VM code for executing s2
label L2
...
```
* while 제어 흐름 구조 
```
while(cond)
s1 
...
```
* while 의사 VM 코드 
```
label L1
  M code for computing ~(cond)
  if-goto L2
  VM code for executing s1
  goto L1
label L2
...
```

## 1.2 서브루틴 호출 

프로그래밍 언어들마다 특정한 내장형 명령 집합이 있습니다. 
이러한 기본적인 명령 집합을 프로그래머가 자유롭게 고수준 연산들로 확장 가능하다는 점이 현대 개발 언어의 핵심적인 추상화 원리입니다. 
이처럼 정의된 고수준 연산 단위를 서브루틴(또는 프로시저, 함수 메서드 등)라고 부릅니다. 

잘 설계된 프로그래밍 언어는 기본 내장형 멸령들과 유사하한 느낌을 갖습니다. 
그렇기때문에 새로운 서브루틴을 구현하게 된다면 호출자 관점에서 기존의 멍령들고 비슷하게 보이는 게 이상적일 것입니다. 
두 연산을 자연스럽게 결합해서 일관성 있고 읽기 쉬운 코드를 만들 수 있습니다. 

[그림]

서브루틴 호출시에 call 키워드가 쓰인다는 점을 제외하면 내장형 명령 호출과 사용자 정의 서브루틴 호출의 차이는 없습니다. 
두 연산 모두 호출자가 인수를 설정해줘야 하며, 스택에서 인수가 삭제되고 반환값이 스택 최상단에 입력됩니다. 

power 와 같은 서브루틴은 보통 지역변수를 임시 저장소로 활용합니다. 
지역변수는 서브루틴 시작부터 return 시점까지 메모리에 유효하며, return 이후에는 메모리 공간이 해제됩니다. 
만약 서브루틴 안에 서브루틴을 호출하는 구조가 여러번 반복된다면 중첩되는 상황이 점점 복잡해지게 됩니다. 
이렇게 되면 서브루틴 안에서 새로운 서브루틴을 호출한다면, 그 서브루틴이 끝나야 기존의 서브루틴을 진행한다는 특징이 있습니다.
이는 후입선출 구조인 스택의 구조와 맞아떨어지게 됩니다. 
아래의 그림은 서브루틴 내부에서 서브루틴을 호출하는 구조가 여러번 반복되었을 때에 대한 스택 구조를 나타낸 그림입니다. 

[그림]
