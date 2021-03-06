---
title: "[밑바닥부터 만드는 컴퓨팅] 07장 가상 머신 1 : 산술 스텍"
excerpt: "해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 7장 내용을 정리하였습니다. "

categories:
  - 'make_computing_system'
tags:
  - 밑바닥부터 만드는 컴퓨팅
  - 컴퓨터 아키텍쳐
  - 가상머신

toc: true
toc_sticky: true

date: 2022-02-24
last_modified_at: 2022-02-24
---

# 개요 

해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 7장 내용을 정리하였습니다.

# 1. 배경

# 1.1 가상 머신 패러다임 

고수준 언어가 컴퓨터에서 실행되려면 먼저 기계어로 번역되어야 합니다. 
이 과정을 컴파일이라고 부르는데, 이는 상당히 복잡한 프로세스입니다. 
고수준 언어를 대상 기계어로 번역하려면 별도의 컴파일러를 따로 만들어야 합니다. 
그렇기때문에 고수준 언어와 기계어의 특성에 따라 수많은 컴파일러가 생겨야 합니다. 

컴파일러에서 기계어의 종속성을 분리하는 방법 중 하나는 전체 컴파일 과정을 두 단계로 나누는 것입니다. 
이 단계는 고수준 언어와 기계어의 중간 단계입니다. 
즉, 고수준 언어는 중간단계 언어로 번역되고, 중간단계 언어에서 각 하드웨어에 맞게 기계어로 변환하는 것입니다. 

먼저 먼저 중간단계 언어를 명령어로 하는 가상 머신을 정의합니다. 
그리고 컴파일러를 `고수준 언어 -> 중간 단계 언어`, `중간 단계 언어 -> 기계어` 이 두 단계로 나눕니다. 

이러한 컴파일 모델은 여러종류의 컴파일러에 적용되어 있습니다. 
많이 사용되는 자바를 예로 들면 `javac`라는 컴파일러를 이용하여 자바 코드를 바이트코드로 변환합니다. 
`.java` 파일을 컴파일하였을때 나오는 `.class` 파일이 바이트코드로 변환된 파일입니다. 
그리고 바이트코드는 JVM이라는 가상머신에서 동작하게 됩니다. 
JVM은 바이트코드를 기계어로 변환하여 실행하는 역할을 하게 됩니다.

이러한 가상 머신 언어 개념은 실용적이며 몇몇 장점을 가지고 있습니다. 
먼저 가상머신만 바꾸면 여려 플랫폼에서 실행할 수 있다는 점입니다. 
다음으로 여러 언어의 컴파일러들이 같은 VM 백엔드를 공유함으로써 코드를 공유하고 운용하기 편하다는 점힙니다. 

가상머신의 또 다른 장점은 모듈성입니다. 
VM 구현이 효율적으로 될대마다 해당 언어로 번역되는 모든 프로그램이 개선된다는 점입니다. 

아래의 그림은 앞써 말한 특장점을 표현하는 그림립니다. 

![image](https://user-images.githubusercontent.com/35713051/158806990-686256c1-5f25-4913-a1c4-7a3ba6d9bac8.png)

## 1.2 스택 머신 모델

VM도 다른 프로그래밍 언어들처럼 산술 연산, 메모리 접근 연산, 프로그램 흐름 제어 연산, 서브루틴 호출 연산으로 구성됩니다. 
언어를 구현하는 방법론은 어려가지 있지만, 이 중 핵심은 `VM의 연산자와 피연산자와 결과값을 어디에 저장하는가` 입니다. 
스택 데이터 구조를 사용하는 방법이 가장 깔끔할 것입니다. 

스택 머신 모델에서 산술 명령은 스택의 최 상단에서 피연산자를 꺼내고(pop), 그 결과를 다시 스택의 최 상단에 넣습니다.(push)
그 외의 다른 명령들의 경우 스택의 최상단과 지정된 메모리 주소 사에에 데이터가 이동됩니다. 
이 간단한 스택 연산으로 산술 및 논리 연산도 구현이 가능합니다. 
또한 어떠한 프로그래밍 언어로 작성되었더라도 동일한 기능을 하는 스택 머신 프로그램으로 번역이 가능합니다. 

### 1.2.1 기본 스택 연산 

스택은 push와 pop을 기본 연산으로 하는 자료구조입니다. 
![image](https://user-images.githubusercontent.com/35713051/158807066-0eb25fa2-b507-4cec-8f1e-ac986a9c344d.png)

스택 머신은 기존의 메모리 접근방식과 다릅니다. 
먼저 스택은 최상단에 있는 한 항목씩만 접근이 가능합니다. 
또한 스택 읽기는 손식 연산입니다. 
스택에서 pop 하는순간 자로구조에서 사라지기 때문입니다. 
일반적인 메모리 구조에서는 메모리에 읽을때는 메모리에 변화가 없다는 점이 차이가 있습니다. 
또한 기존의 메모리 구조는 값을 쓰면 기존 값을 덮어쓰는 반면 스택구조는 기존의 데이터를 유지하고 스택의 맨 위에 값을 쓰게 됩니다. 

![image](https://user-images.githubusercontent.com/35713051/158807113-4cd83c9f-f4bb-464e-9418-c2abb0d63af8.png)
예를 들어 덧셈의 처리는 위의 그림과 같습니다. 
add 연산자 처리를 위해 스택에서 pop을 두번해서 나온 값을 더한 뒤 다시 스택에 push합니다.

![image](https://user-images.githubusercontent.com/35713051/158807139-705f0793-1f40-48c6-81d5-695cc313207c.png)
다른 예로 `d=(2-x)*(y+5)` 라는 식을 계산하는 방법이다.

![image](https://user-images.githubusercontent.com/35713051/158807163-df5d0443-64d7-4aab-b420-e0119b4e5ae7.png)
이번에는 불 표현식인 `if (x>7) or (y=8) then ...` 을 스택 기반으로 계산하는 방법입니다. 


# 2. VM 명세 1부

## 2.1 일반 

해당 책에서 사용하는 가상머신은 스택 기반입니다. 
즉, 모든 연산은 스택 위에서 이뤄집니다. 
또한 함수 기반으로 프로그램이 수행됩니다. 
이 VM 언어에는 정수, 불 대수, 포인터로 쓰이는 16비트 데이터 타입 하나만 정의됩니다. 
이 언어에는 네 가지 종류의 명령으로 구성됩니다. 

* 산술 명령 : 스택에서 산술 및 논리 연산을 수행함
* 메모리 접근 명령 : 스택과 가상 메모리 세그먼트 사이에 데이터를 주고 받는 명령
* 프로그램 흐름 명령 : 조건 및 무조건 분기 연산을 가능하게 함 
* 함수 호출 명령 : 함수를 호출하고 결과를 반환함

가상머신은 크게 두 단계로 나눠집니다. 
해당 장에서는 산술 명령 및 메모리 접근 명령을 정의하고, 그 명령들을 구현한 기본 VM 번역기를 만드는 것을 목표로 합니다. 
다음 장에서는 프로그램 흐름 제어 명령 및 함수 호출 명령을 정의하여 가상 머신을 확장할 예정입니다. 

## 2.2 산술 및 논리 명령 

이 VM 언어에는 9개의 스택 기반 산술 및 논리 명령들이 있습니다. 
이 명령 중에 7개는 2항 명령입니다. 
즉, 스택에서 두개의 항목을 꺼네서(pop) 연산한 후 그 결과를 다시 스택에 넣습니다.(push)
나머지 두개는 단항 명령으로 스택에서 하나의 항목을 꺼네서(pop) 연산한 후 그 결과를 다시 스택에 넣습니다.(push)

| 명령 | 반환값 | 설명 | 
| --- | --- | --- |
| add | x + y | 정수 덧셈 |
| sub | x - y | 정수 뺄셈 |
| neg | -y | 산술 부정 |
| eq | true if x = y, else false | 같음 |
| gt | true if x > y, else false | ~ 초과 (greater than)|
| lt | true if x < y, else false | ~ 미만 (less than) |
| and | x And y | 비트 단위 |
| or | x Or y | 비트 단위 |
| not | Not y| 비트 단위 |

## 2.3 메모리 접근 명령 

이 VM 에서는  8개의 가상 메모리 세그먼트들을 조작하는 메모리 접근 명령을 수행합니다. 

![image](https://user-images.githubusercontent.com/35713051/159123283-166f36eb-62c4-4a6a-9730-5673a38cb053.png)

### 2.2.1 메모리 접근 명령 

모든 메모리 세그먼트는 두 개의 동일한 명령으로 접근 가능합니다. 

* push segment index : segment[index] 값을 스택에 넣는다 (push)
* pop segment index : 최상단 스택에서 값을 꺼내서(pop) segment[index] 에 저장한다

여기서 segment는 8개의 세그먼트 이름 중 하나이며, index는 음수가 아닌 정수입니다. 
예를 들어 `push argment 2` 다음 `pop local 1` 명령은 함수의 세번째 인수 값을 함수의 두번째 지역 변수에 저장하라는 의미가 됩니다. 

아래는 VM파일, VM 함수 및 가상 메모리 세그먼트간의 관계를 나타낸 그림입니다. 

![image](https://user-images.githubusercontent.com/35713051/159123299-292c640f-f11f-410d-bd52-7ccb5574d915.png)

VM의 push와 pop 명령을 명시적으로 관리하는 8개의 메모리 세그먼트 외에도 VM은 스택과 힙이라고 하는 두개의 두개의 데이터 구조를 내부에서 관리합니다. 

힙은 VM 뒷단에 존제하는 메모리 공간으로 객체와 배열 데이터를 저장하는데 사용하는 RAM 영역입니다. 

## 2.4 프로그램 흐름과 함수 호출 명령

본 VM에서 자세히 설명해야할 6가지 명령이 있습니다. 
이는 아래와 같으며 해당 내용은 다음장에 설명할 예정입니다. (책에 그렇게 써있음 ㅠㅠ)

```
프로그램 흐름 명령
label symbol    // 레이블 선언
goto symbol     // 무조건 분기
if-goto symbol  // 조건 분기

함수 호출 명령
function functionName n Locals // 함수 선언. 함수의 지역 변수 개수를 지정함.
call functionName nargs        // 함수 호출. 함수의 인수 개수를 지정함.
return                         // 호출하는 함수로 제어를 되돌림
```

## 2.5 잭 VM 핵 플랫폼의 프로그램 요소들

![image](https://user-images.githubusercontent.com/35713051/159123310-c3db924f-8e7d-458e-adb1-060de3f12cdb.png)

위의 그램은 jack 언어가 컴파일되는 과정을 나타냅니다. 
jack 언어는 9장에서 설명합니다. 

잭 클래스는 각각 하나 이상의 메서드로 구성됩니다. 
잭 컴파일러가 n 개의 클래스 파일이 들어있는 디렉터리에 적용되면 n개의 VM 파일이 생성됩니다. 

다음으로는 VM 번역기가 VM 파일들이 있는 디렉토리에 적용되어 하나의 어셈블리 프로그램이 생성됩니다. 

번역된 어셈블리 코드는 어셈블러에 의해 2진 코드로 번역되어 실행 가능한 프로그램으로 변경합니다. 

## 2.6 VM 프로그래밍 예제

해당 절에서는 일반적인 프로그래밍 작업들이 어떻게 VM 추상화로 표현할 수 있는지를 설명합닏닫. 

### 2.6.1 일반적인 산술 작업 

* C 언어
```c
int mult(int x, int y) {
  int sum;
  sum = 0;
  for(int j = y; j != 0; j--)
    sum += x; // 514
  return sum;
}
```

* 1차 근사 
```
function mult
  args x, y
  vars sum, j
  sum = 0
  j = y
loop:
  if j = 0 goto end
  sum = sum + X
  j = j - 1
  goto loop
end:
  return sum
```

* 의사 VM 코드 
```
function mult(x,y)
  push 0
  pop sum
  push y
  pop j
label loop
  push 0
  push j
  eq
  if-goto end
  push sum
  push x
  add
  pop sum
  push j
  push 1
  sub
  pop j
  goto loop
label end
  push sum
  return
```

* 최종 VM 코드
```
function mult 2  // 2개의 지역 변수
  push constant 0
  pop local 0 // sum=0
  push argument 1
  pop local 1 // j=y
label loop
  push constanta
  push local 1
  Eq
  if-goto end // if j=0 goto end
  push local 0
  push argumento
  Add
  pop local 0 // SUM=SUM+X
  push local 1
  push constant 1
  Sub
  pop local 1 1/ j=j-1
  goto loop
label end
  push local 0
  return // return sum

```

![image](https://user-images.githubusercontent.com/35713051/159123330-667385a9-81c9-4599-83b2-6de35526d2e8.png)

### 2.6.2 베열 처리 

배열은 인덱스가 매겨진 객체들의 집합입니다. 
`int bar[10]` 배열에 각 10개의 정수값이 입력되어있고, bar의 주소가 4315라고 가정해보겠습니다. 

만약 `bar[2] = 19` 를 연산하려면 어떻게 해야될까요?

* VM 코드 
```
// bar 배열이 이 고수준 프로그램에서 처음 선언된 지역 변수라 가정하자.
// 다음 M 코드는 bar[2]=19, 즉 *(bar+2)=19를 구현한 것이다.
push local o // bar의 시작 주소를 얻는다.
push constant 2
add
pop pointer 1 // that의 시작 주소를 (bar+2)로 설정한다.
push constant 19
pop that 0 // *(bar+2)=19
...
```

![image](https://user-images.githubusercontent.com/35713051/159123347-00a6b599-216a-4931-b7ce-1d8e4e2bc53a.png)

![image](https://user-images.githubusercontent.com/35713051/159123354-d4c0e7d6-e48b-4221-9021-fe0f1b35d754.png)


### 2.6.3 객체 처리 

고수준 프로그래머 관점에서 객체는 데이터와 관련 코드를 캡슐화한 개체입니다. 
이때 실제 구현을 보면 각 객체 인스턴스는 객체의 필드 값들을 나타내는 숫자들로 직렬화된 RAM 상의 데이터입니다. 

예시로 스크린에서 공들을 저글링하는 애니메이션 프로그램을 생각해보겠습니다. 
Ball 객체의 특성들로 x, y, radius, color이라는 정수 필드들이 있다고 하겠습니다. 
그리고 프로그램에서는 Ball 객체를 하나 생성하고 b라고 이름을 붙였습니다. 

객체도 역시 RAM에 저장됩닏다. 
아래의 그림은 객체 b가 RAM의 3012 ~ 3015 에 할달되었다고 하였을때의 구조입니다. 

![image](https://user-images.githubusercontent.com/35713051/159123360-b515d895-b8eb-4598-87f8-0a848d4269cf.png)

![image](https://user-images.githubusercontent.com/35713051/159123364-c7cce036-1e5f-4f10-9ea7-f32af7f43578.png)

```
// 객체 b와 정수 r이 이 함수의 처음 두 인수로 전달된다고 가정한다. 
// 다음 코드는 b.radius=r을 구현한 코드다.
push argument 0 // b의 시작 주소를 얻는다.
pop pointer 0 // 이 세그먼트가 b를 가리키게 한다. push argument 1 // r의 값을 얻는다.
pop this 2 // b의 세 번째 필드에 을 설정
...
```
