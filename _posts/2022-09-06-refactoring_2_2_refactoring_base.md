---
title: "6장 기본적인 리팩터링"
excerpt: "리팩터링 2판의 6장의 기본적인 리팩터링 내용을 다룹니다."

categories:
  - 'refactoring_2'
tags:
  - refactoring

date: 2022-09-20
last_modified_at: 2022-09-20
---

# 개요 

1장에서 중간을 생략하고 6장에서 시작하는 이유는 본격적인 리팩터링 기법을 정리하기 위함입니다. 

2 ~ 5 장 까지는 리팩터링에 대한 유래, 예시 등이 담겨져 있으니 서적을 구매하여 보시는 것도 추천드립니다. 

# 함수 추출하기

* 리팩터링 전 
```python 
def printOwing(invoice):
    printBanner()
    outstanding = caculateOutstanding()

    # 세부 사항 출력 
    print('고객명 : {}'.format(invoice.customer))
    print('채무액 : {}'.format(outstanding))


```

* 수정 후 

```python 

def printDetails(outstanding):
   # 세부 사항 출력 
    print('고객명 : {}'.format(invoice.customer))
    print('채무액 : {}'.format(outstanding))

def printOwing(invoice):
    printBanner()
    outstanding = caculateOutstanding()

     printDetails(outstanding)

}
```

## 배경 

함수 추출하기는 코드 조각을 찾아 무슨 일을 하는지 파악한 다음, 독립된 함수로 추출하고 목적에 맞는 이름을 붙입니다. 

함수를 짧게 만들면 함수 호출이 많아지게 됩니다. 
이로 인해 성능이 느려질까 걱정하는 사람도 있습니다. 
하지만 함수가 짧으면 캐싱하기가 쉽기에 컴파일러가 최적화하는데 더 유리하다고 합니다. 

이러한 짧은 함수는 이름을 잘 지어야 효과가 좋기 때문에 함수 이름에 신경을 써야 합니다. 

## 절차 


1. 함수를 새로 만들고 목적을 잘 드러내는 이름을 붙임 
2. 추출할 코드를 원본 함수에서 복사하여 새 함수를 붙여넣음
3. 추출한 코드 중 원본 함수의 지역변수를 참조하거나 추출한 함수의 유효 볌위를 벗어나는 변수는 없는지 검사함
4. 변수를 다 처리했다면 컴파일함 
5. 원본 함수에서 추출한 코드 부분을 새로 만든 함수를 호출하는 문장으로 바꿈 
6. 테스트 진행 
7. 이와 같은 코드가 또 있는지 확인하고 함수를 추철할지 검토 

# 함수 인라인하기 

* 리팩터링 전 

```python
def getRating(driver):
    return moreThenFiveLateDeliveries(driver) ? 2 : 1


def moreThenFiveLateDeliveries(driver):
    return driver.numberOfLateDeliveries > 5
```

* 리팩터링 후 
```python
def getRating(driver):
    return (driver.numberOfLateDeliveries > 5) ? 2 : 1


def moreThenFiveLateDeliveries(driver):
    return driver.numberOfLateDeliveries > 5
```

## 배경 

해당 서적에서는 목적이 분명히 드러나는 이름의 짤막한 함수를 이용하기를 권합니다. 
그래야 코드가 명료해지고 이해하기 쉬워지기 때문입니다. 

리팩터링 과정에서 잘못 추출된 함수들을 다시 인라인해줍니다. 
잘못 추출된 함수들을 원래 함수로 합친다음 필요하면 원하는 형태로 다시 추출하는 것입니다. 

## 절차 

1. 다형 메서드인지 확인
  * 서브 클래스에서 오버라이드하는 메서드는 인라인하면 안됨 
2. 인라인할 함수를 호출하는 곳으로 모두 찾음 
3. 각 호출문을 함수 본문으로 교체 
4. 하나씩 교체할 때마다 테스트함 
5. 함수 정의를 삭제 


# 변수 추출하기 

* 리팩터링 전 

```python
return order.quantity * order.itemPrice - max(0, order.quantity-500) * order.itemPrice * 0.05 + min(order.quantity * order.itemPrice * 0.1, 100)
```

* 리팩터링 후 

```python
basePrice = order.quantity * order.itemPrice 
quantityDiscount = max(0, order.quantity-500) * order.itemPrice * 0.05
shipping = min(order.quantity * order.itemPrice * 0.1, 100)
return basePrice - quantityDiscount  + shipping 
```

## 배경 

표현식이 너무 복잡하면 이해하기가 어렵습니다. 
이럴때는 지역변수를 활용하여 표현식을 쪼개면 관리하기 더 편하게 됩니다. 
복잡한 로직을 구성하는 단계마다 이름을 붙여 코드의 목적을 더 명확하게 드러낼 수 있습니다. 

또한 이렇게 추가된 변수들은 디버깅 과정에서 도움이 됩니다. 
breaking point를 지정할 수 있기 때문입니다. 

## 절차 

1. 추출하려는 표현식에 부작용이 없는지 확인 
2. 불변 변수를 하나 선언하고 이름을 붙일 표현식의 복제본을 대입함
3. 원본 표현식을 새로 만든 변수로 교체 
4. 테스트
5. 표현식을 여러 곳에서 사용한다면 각각을 새로 만든 변수로 교체함 

# 함수 선언 바꾸기 

* 리팩터링 전 

```python 
    def circum(radius):
        ……
        return
```

* 리팩터링 후 

```python 
    def circumference(radius):
        ……
        return
```


## 배경 

함수는 프로그램을 작은 부분으로 나누는 주된 수단입니다. 
그렇기 때문에 함수의 이름은 매우 중요한 요소 중 하나입니다.

함수의 이름이 좋으면 함수의 구현 코드를 보지 않고 호출 부분만 봐도 무슨 일을 하는지 유추가 가능합니다. 

함수의 매개변수도 마찬가지입니다. 매개변수는 함수를 사용하는 문맥을 설정하기 때문에 이름을 잘 정해주는 것이 좋습니다. 

## 절차
함수 선언을 바꾸는 간단한 절차는 다음과 같습니다. 


1. 매개변수를 제거하려든 먼저 함수 본문에서 제거 대상 매개변수를 참조하는 곳이 없는지 확인한다. 
2. 매서드 선언을 원하는 형태로 바꾼다 
3. 기존 메서드 선언을 참조하는 부분을 모두 찾아서 바뀐 형태로 수정한다. 
4. 테스트한다. 

변경할 게 둘 이상이면 나눠서 처리하는 편이 나을 때가 많습니다. 
이의 경우 이름 변경과 매개변수 추가에 대해서 각각 독립적으로 처리하는 것이 좋습니다. 

아래는 마이그레이션 절차입니다. 

1. 이어지는 추출 단계를 수월하게 만들어야 한다면 함수의 본문을 적절히 리팩터링한다.
2. 함수 본문을 새로운 함수로 추출한다.
  * 새로 만들 함수 이름이 기존 함수와 같다면 일단 검색하기 쉬운 이름을 임시로 붙여둔다.
3. 추출한 함수에 매개변수를 추가해야 한다면 ‘간단한 절차’를 따라 추가한다.
4. 테스트한다. 
5. 기존 함수를 인라인한다.
6. 이름을 임시로 붙여뒀다면 함수 선언 바꾸기를 한 번 더 적용해서 원래 이름으로 되돌린다. 
7. 테스트한다.

# 변수 캡슐화 하기

* 리팩터링 전 

```python
defaultOwner = {firstName: "마틴", lastName: "파울러"}
```

* 리팩터링 후 

```python
defaultOwnerData = {firstName: "마틴", lastName: "파울러"}

def defaultOwner():
    return defaultOwnerData

def setDefaultOwner(arg):
    global defaultOwnerData
    defaultOwnerData = arg
```

## 배경 

데이터 캡슐화는 데이터를 변경하고 사용하는 코드를 감시할 수 있는 확실한 통로가 되어주기 때문에 데이터 변경 전 검증이나 변경 후 추가 로직을 쉽게 넣을 수 있습니다. 
객체 지향에서 객체의 데이터를 항상 private로 유지하는 이유가 여기에 있습니다. 
public를 private로 변경하고 캡슐화하여 가시 범위를 제한하는 것이 좋습니다. 

## 절차 

1. 변수로의 접근과 갱신을 전담하는 캡슐화 함수들을 만든다. 
2. 정적 검사를 수행한다. 
3. 변수를 직접 참조하던 부분을 모두 적절한 캡슐화 함수 호출로 바꾼다.
4. 변수의 접근 범위를 제한한다. 
5. 테스트한다. 
