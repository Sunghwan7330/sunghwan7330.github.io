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
