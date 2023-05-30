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
6. 클래스에서 원본 데이터를 반환하는 접근자와 원본 레코드를 반환하는 함수들을 제거한다. 7. 테스트한다. 
