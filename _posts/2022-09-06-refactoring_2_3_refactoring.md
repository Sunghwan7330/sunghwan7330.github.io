---
title: "7장 캡슐화"
excerpt: "리팩터링 2판의 7장의 캡슐화 리팩터링 내용을 다룹니다."

categories:
  - 'refactoring_2'
tags:
  - refactoring

date: 2023-05-29
last_modified_at: 2022-05-29
---

# 개요 

모듈을 분리하는 가장 중요한 기준은 시스템에서 각 모듈이 자신을 제외한 다른 부분에 드러내지 않아야 할 비밀을 얼마나 잘 숨기는데 있습니다. 

클래스는 본래 정보를 숨기기 좋게 설계할 수 있습니다. 

본 장에서는 클래스에서 본래의 정보를 숨기기 위한 캡슐화 방법에 대해 알아보려 합니다. 

# 레코드 캡슐화하기 

* 리팩터링 전 

```python 
organization = {name: “애크미 구스베리”, country: “GB”}
```

* 리팩터링 후 

```python 
class Orgnization:

    def __init__(self, data):
        self.__name = data.name
        self.__country = data.country

    def getName(self):
        return self.__name

    def setName(self, name):
        self.__name = name

    def getCountry(self):
        return self.__country

    def setCountry(self, country):
        self.__country = country
```

## 배경 

대부분의 프로그래밍 언어는 데이터 레코드를 표현하는 구조를 제공합니다. 
레코드는 연관된 여러 데이터를 직관적인 방식으로 묶을 수 있어 각각을 취급할 때 보다 훨씬 의미있는 단위로 전달할 수 있게 됩니다. 

## 절차 

1. 레코드를 담은 변수를 캡슐화한다.
2. 레코드를 감싼 단순한 클래스로 해당 변수의 내용을 교체한다.
3. 테스트한다.
4. 원래 레코드 대신 새로 정의한 한 클래스 타입의 객체를 반환하는 함수들을 새로 만든다.
5. 레코드를 반환하는 예전 함수를 사용하는 코드를 4.에서 만든 새 함수를 사용하도록 바꾼다.
6. 클래스에서 원본 데이터를 반환하는 접근자와 원본 레코드를 반환하는 함수들을 제거한다. 
7. 테스트한다. 

# 컬렉션 캡슐화하기 

* 리팩터링 전 

```python 
class Person :
    def getCourses(self)
        return self.__courses

    def setCourses(self, aList):
        self.__courses = aList
```

* 리팩터링 후 

```python 
class Person :
    def getCourses(self)
        return self.__courses.slice()

    deff addCourse(self, aCourse):
        ……

    def removeCourse(self, aCourse):
        ……
```

## 배경 

컬렉션 변수의 접근을 캡슐화하면서 getter가 컬렉션 자체를 반환하도록 한다면, 그 컬렉션을 감싼 클레스가 모르게 컬렉션의 원소들이 변경될 수 있습니다. 

이러한 문제를 방지하기 위해 흔히 add() 와 remove()라는 이름의 컬렉션 변경자 메서드를 사용합니다. 이렇게 컬렉션을 소유한 클래스를 통해서만 원소를 변경하도록 하면 프로그램을 개선하면서 컬렉션 변경 방식도 원하는 대로 수정할 수 있습니다. 

## 절차 

1. 컬렉션 변수를 캡슐화한다. 
2. 컬렉션에 원소를 추가 / 제거하는 함수를 추가한다. 
3. 정적 검사를 수행한다. 
4. 컬렉션을 참조하는 부분을 모두 찾는다. 컬렉션의 변경자를 호출하는 코드가 모두 앞에서 추가 / 제거 함수를 호출하도록 수정한다. 
5. 컬렉션 getter를 수정해서 원본 내용을 수정할 수 없는 읽기 전용 프락시나 복제본을 반환하게 한다. 
6. 테스트한다. 

# 클래스 추출하기 

* 리팩터링 전 

```python 
class Person:
    getOfficeAreaCode(self):
        return self.__officeAreaCode

    getOfficeNumber(self):
        return self.__officeNumber
```


* 리팩터링 후 

```python 
class Person:
    getOfficeAreaCode(self):
        return self.__telphoneNumber.getAreaCode()

    getOfficeNumber(self):
        return self.__telphoneNumber.getNumber()

class TelephoneNumber():
    getAreaCode(self):
        return self.__areaCode

    getNumber(self):
        return self.__number
        
```

## 배경

메서드와 데이터가 너무 많은 클래스는 이해하기 쉽지 않으니 잘 살펴보고 적절히 분리하는것이 좋습니다. 
만약 일부 데이터와 메서드를 따로 묶을 수 있다면 분리하는것이 좋습니다. 
제거해도 다른 필드나 메서드들이 논리적으로 문제가 없다면 분리할 수 있다는 뜻이 됩니다. 

## 절차 

1. 클래스의 역할을 분리할 방법을 정한다 
2. 분리될 역할을 담당할 클래스를 새로 만든다 
3. 원래 클래스의 생성자에서 새로운 클래스의 인스턴스를 생성하여 필드에 저장해둔다. 
4. 분리될 역할에 필요한 필드들을 새 클래스로 옮긴다 
5. 매서드들도 새 클래스로 옮긴다
6. 양쪽 클래스의 인터페이스를 살펴보면서 불필요한 메서드를 제거하고, 이름도 새로운 환경에 맞게 바꾼다 
7. 새 클래스를 외부로 노출할지 결정한다 

# 클래스 인라인하기 

* 리팩터링 전 

```python 
class Person:
    getOfficeAreaCode(self):
        return self.__telphoneNumber.getAreaCode()

    getOfficeNumber(self):
        return self.__telphoneNumber.getNumber()

class TelephoneNumber():
    getAreaCode(self):
        return self.__areaCode

    getNumber(self):
        return self.__number    
```


* 리팩터링 후 

```python 
class Person:
    getOfficeAreaCode(self):
        return self.__officeAreaCode

    getOfficeNumber(self):
        return self.__officeNumber

```

## 배경 

클래스 인라인하기는 클래스 추출하기를 거꾸로 돌리는 리팩터링입니다. 
더 이상 제 역할을 못해서 그대로 두면 안되는 클래스를 인라인 하는게 좋습니다. 
역할을 옮기는 리팩터링을 하고 난 뒤 특정 클래스에 남는 역할이 거의 없을 때 이러한 현상이 자주 생기는데, 이때 클래스를 인라인하는게 보기 좋습니다. 

## 절차 

1. 소스 클래스의 각 public 메서드에 대응하는 메서드들을 타깃 클래스에 생성한다. 이 메서드들은 단 순히 작업을 소스 클래스로 위임해야 한다. 
2. 소스 클래스의 메서드를 사용하는 코드를 모두 타깃 클래스의 위임 메서드를 사용하도록 바꾼다. 
3. 소스 클래스의 메서드와 필드를 모두 타깃 클래스로 옮긴다.

# 알고리즘 교체하기 

* 리팩터링 전

```python
def foundPerson(self, people):
    for i in range(len(people)):
        if people[i] == ‘Don’:
            return ‘Don’
        if people[i] == ‘John’:
            return ‘John’
        if people[i] == ‘Kent’:
            return ‘Kent’
    reurn ‘’
```



* 리팩터링 후 

```python
def foundPerson(self, people):
    candidates = [‘Don’, ‘John’, ‘Kent’]
    return people if people in candidates else ‘’
```

## 배경 

어떤 목적을 달성하는 방법에는 여러가지가 있고, 그 중에서는 다른 것보다 더 쉬운 방법이 있기 마련입니다. 
리팩터링을 하면 복잡한 대상을 단순한 단위로 나눌 수 있지만, 떄로는 알고리즘 전체를 걷어내고 훨씬 간단한 알고리즘으로 바꿔야 할 때가 있습니다. 

이러한 작업을 진행하기 전에 메서드가 가능한 잘게 나눴는지 확인할 필요가 있습니다. 
거대하고 복잡한 알고리즘을 교체하기란 상당히 어려우니 알고리즘을 간소화하는 작업부터 해야 교체가 쉬워집니다. 

## 절차 

1. 교체할 코드를 함수 하나에 모은다 .
2. 이 함수만을 이용하여 동작을 검증하는 테스트를 마련한다.
3. 대채할 알고리즘을 준비한다.
4. 정적 검사를 수행한다.
5. 기존 알고리즘과 새 알고리즘의 결과를 비교하는 테스트를 수행하한다.
