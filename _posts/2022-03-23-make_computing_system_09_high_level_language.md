---
title: "[밑바닥부터 만드는 컴퓨팅] 9장 고수준 언어"
excerpt: "해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 9장 내용을 정리하였습니다. "

categories:
  - 'make_computing_system'
tags:
  - 밑바닥부터 만드는 컴퓨팅
  - 고수준 언어

toc: true
toc_sticky: true

date: 2022-03-23
last_modified_at: 2022-03-23
---

해당 장에서는 고수준 프로그램을 작성할 수 있는 잭(jack)이라는 언어를 소개합니다. 
잭은 간단한 객체지향 언어입니다. 
자바나 C# 같은 언어와 비슷하지만 문법이 더 단순하며, 상속을 지원하지 않습니다. 

10장과 11장에서는 잭 프로그램을 VM 코드로 변역하는 컴파일러를 만들고, 12장에서는 잭/핵 플랫폼의 간단한 운영체제를 작성할 예정입니다. 

# 1. 배경 

## 1.1 예제 1: Hello world

잭 언어는 Main.main 함수에서 시작됩니다. 
Main이라는 클래스 안에 main이라는 메서드가 있어야 합니다. 

```
/* Hello World 프로그램 */ 
class Main { 
  function void main() { 
    /* 표준 라이브러리를 이용해서 텍스트를 출력한다. */ 
    do Output.printString("Hello World"); 
    do Output.println(); // 새 라인 
    return; 
  } 
}
```
## 1.2 절차적 프로그래밍과 배열 처리 

```
/* 정수열의 평균을 계산한다. */ 
class Main { 
  function void main() { 
    var Array a; 
    var int length; 
    var int i, sum; 

    let length = Keyboard. readInt("How many numbers? "); 
    let a = Array.new(length); // 배열을 구성한다. 
    let i = 0; 

    while (i < length) { 
      let a[i] = Keyboard. readInt("Enter the next number: "); 
      let sum = sum + a[i]; 
      let i = i + 1; 
    } 

    do Output.printString("The average is: "); 
    do Output.printInt(sum / length); 
    do Output.println(); 
    return; 
  } 
}
```

잭 프로그램은 표준 라이브러리에 내장된 Array 클래스를 통해 배열을 선언합니다. 
잭 언어의 배열에는 타입이 없습니다. 
정수, 객체 등등 어떤 것이라도 넣을 수 있습니다. 

## 1.3 예제 3: 추상 데이터 타입 

프로그래밍 언어는 기본 데이터 타입이 정해져 있는데, 잭은 int, char, boolean 세가지를 지원합니다. 
추가로 필요한 경우 직접 클래스를 만들어 사용할 수 있습니다. 

### 1.3.1 클래스 인터페이스 정의하기 

이번 예시는 분수를 추상화하려 합니다. 

```
// Fraction은 n/m을 표현하는 객체다. (n, m은 정수) 
field int numerator, denominator // Fraction 객체 속성 
constructor Fraction new(int a, int b) // 새로운 Fraction 객체를 반환한다. 
method int getNumerator() // 이 분수의 분자를 반환한다. 

method int getDenominator() // 이 분수의 분모를 반환한다. 
method Fraction plus (Fraction other) // 이 분수와 또 다른 분수의 합을 분수 객체로 반환한다. 
method void print() // 이 분수를 "분자/분모"  형식으로 출력한다.  필요한 분수 관련 기능을 여기에 추가한다
```

잭에서 현재 객체 수준의 연산은 메서드로 표현되고, 클래스 수준의 연산은 함수로 표현됩니다. 
