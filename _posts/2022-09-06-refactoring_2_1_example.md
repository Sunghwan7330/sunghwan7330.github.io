---
title: "1장 리팩터링 첫 예시"
excerpt: "리팩터링 2판의 1장의 예시 내용을 다룹니다."

categories:
  - 'refactoring_2'
tags:
  - refactoring

date: 2022-09-06
last_modified_at: 2022-09-06
---

# 개요 

해당 장에서는 예시 코드를 리팩터링 하면서 리팩터링의 중요성을 파악해보려 합니다. 

해당 서적에서는 자바스크립트를 예시로 설명합니다. 

저는 자바스크립트를 사용할 일이 없기 때문에 예제 코드를 파이썬으로 변환하여 설명드리려 합니다. 

자바스크립트를 사용하시는 분들은 책을 직접 보시는 것도 추천드립니다. (유명한 서적기에)

예시는 아래의 레퍼지토리에 업로드하였습니다. 

* https://github.com/Sunghwan7330/my_study/tree/master/refactoring_example/python

# 예시 코드 

예시 코드는 다양한 연극을 외주로 받아 공연하는 극단에서 공연 요청이 들어왔을 때 공연 청구서를 출력하는 예시입니다. 
현재 이 극단은 비극과 희극만 공연하고, 공연료와 별개로 포인트를 지급하여 다음번 의뢰시 공연 할인을 받을 수 있도록 하려 합니다. 

서적에서는 json 파일로 데이터를 입력하였는데, 저는 파이썬 코드로 입력하였습니다. 
`palys`에는 극단 정보, `invoices.json` 에는 공연료 청구서 내용을 입력하였습니다. 
이후 `statement` 함수에서 청구서 내용을 return 받도록 하였습니다. 

```python
def main():
    invoice = {}
    invoice = {
        "customer": "BigCo",
        "performances": [
            {
                "playID": "hamlet",
                "audience": 55
            },
            {
                "playID": "as-like",
                "audience": 35
            },
            {
                "playID": "othello",
                "audience": 40
            }
        ]
    }
    plays = {
            "hamlet": {"name": "Hamlet", "type": "tragedy"},
            "as-like": {"name": "As You Like It", "type": "comedy"},
            "othello": {"name": "Othello", "type": "tragedy"}
    }
    print(statement(invoice, plays))
    return
```

공연료를 계산하여 반환하는 `statement` 함수는 아래와 같습니다. 

```python
def statement(invoice, plays):
    totalAmount = 0
    volumeCredits = 0
    result = '청구 내역 (고객명: {})\n'.format(invoice['customer'])
    creditFormat = ',.2f'
    for perf in invoice['performances']:
        play = plays[perf['playID']]
        thisAmount = 0
        if play['type'] == 'tragedy': #비극
            thisAmount = 40000;
            if perf['audience'] > 30:
                thisAmount += 1000 * (perf['audience'] - 30)
        elif play['type'] == "comedy": #비극
            thisAmount = 30000
            if perf['audience'] > 20:
                thisAmount += 10000 + 500 * (perf['audience'] - 20)
            thisAmount += 300 * perf['audience'];
        else:
            return '알 수 없는 장르: %s' % play['type']

        # 포인트를 적립한다.
        volumeCredits += max(perf['audience'] - 30, 0);

        # 희극 관객 5명마다 추가 포인트를 제공한다.
        if "comedy" == play['type']:
            volumeCredits += perf['audience'] // 5
        # 청구 내역을 출력한다.
        result += ' {}: ${} ({}석)\n'.format(play['name'], format(thisAmount//100, creditFormat), perf['audience'])
        totalAmount += thisAmount;

    result += '총액: ${}\n'.format(format(totalAmount//100, creditFormat));
    result += '적립 포인트: {}점\n'.format(volumeCredits);
    return result;
```

이 코드의 실행 결과는 아래와 같습니다. 

```
청구 내역 (고객명: BigCo)
 Hamlet: $650.00 (55석)
 As You Like It: $580.00 (35석)
 Othello: $500.00 (40석)
총액: $1,730.00
적립 포인트: 47점
```

# statement 함수 쪼개기 

## amountFor 함수로 분리

위 코드에서 장르별로 나눈 if문이 눈에 띕니다. (원 코드에서는 switch 코드)

이러한 점은 코드를 분석하여 얻은 정보입니다. 그렇기 때문에 다음에 다시 볼때 내용을 잊고 다시 분석하게 될 가능성이 높습니다. 
이러한 부분을 함수로 따로 추출해줍니다. 

```python
def amountFor(aPerformance, play): # 값이 바뀌지 않는 변수는 파라메타로 전달
    result = 0  # 명확한 이름으로 변경
 
    if play['type'] == 'tragedy': #비극
        result = 40000
        if aPerformance['audience'] > 30:
            result += 1000 * (aPerformance['audience'] - 30)
    elif play['type'] == "comedy": #비극
        result = 30000
        if aPerformance['audience'] > 20:
            result += 10000 + 500 * (aPerformance['audience'] - 20)
        result += 300 * aPerformance['audience']
    else:
        return -1
   
    return result  # 함수 안에서 값이 바뀌는 변수 반환
 
```
기존함수의 내용을 복사하여 amountFor 이라는 새로운 함수를 생성하였습니다. 

함수를 조금 더 직관적으로 보이게 하기 위해 아래의 수정을 반영하였습니다. 

* thisAmount 변수를 reslut 로 변경
* perf를 aPerformance 로 변경

## play 변수 제거 

다음으로는 play 변수를 제거하려 합니다. 
playFor 함수를 생성해줍니다. 해당 함수는 `aPerformance` 를 입력받아 해당 play를 반환하는 함수입니다. 

```python 
def playFor(aPerformance):
    plays = {
    "hamlet": {"name": "Hamlet", "type": "tragedy"},
    "as-like": {"name": "As You Like It", "type": "comedy"},
    "othello": {"name": "Othello", "type": "tragedy"}
    }
    return plays[aPerformance['playID']]
```
위 함수가 있다면 statement와 amonutFor 에서는 play가 필요없습니다. 
따라서 해당 함수에서 play를 playFor 함수로 변경해줍니다. 

```python 
def amountFor(aPerformance): # 값이 바뀌지 않는 변수는 파라메타로 전달
    result = 0  # 명확한 이름으로 변경
 
    type = playFor(aPerformance)['type']
    if type == 'tragedy': #비극
        result = 40000
        if aPerformance['audience'] > 30:
            result += 1000 * (aPerformance['audience'] - 30)
    elif type == "comedy": #비극
        result = 30000
        if aPerformance['audience'] > 20:
            result += 10000 + 500 * (aPerformance['audience'] - 20)
        result += 300 * aPerformance['audience']
    else:
        return -1
   
    return result  # 함수 안에서 값이 바뀌는 변수 반환
 
 
def statement(invoice):
    totalAmount = 0
    volumeCredits = 0
    result = '청구 내역 (고객명: {})\n'.format(invoice['customer'])
    creditFormat = ',.2f'
    for perf in invoice['performances']:
        thisAmount = amountFor(perf)
 
        # 포인트를 적립한다.
        volumeCredits += max(perf['audience'] - 30, 0)
 
        # 희극 관객 5명마다 추가 포인트를 제공한다.
        if "comedy" == playFor(perf)['type']:
            volumeCredits += perf['audience'] // 5
        # 청구 내역을 출력한다.
        result += ' {}: ${} ({}석)\n'.format(playFor(perf)['name'], format(thisAmount//100, creditFormat), perf['audience'])
        totalAmount += thisAmount
 
    result += '총액: ${}\n'.format(format(totalAmount//100, creditFormat))
    result += '적립 포인트: {}점\n'.format(volumeCredits)
    return result

```

## 적립 포인트 계산 코드 추출하기 

```python
def volumeCreditsFor(perf):
    result = 0
 
    result += max(perf['audience'] - 30, 0)
    # 희극 관객 5명마다 추가 포인트를 제공한다.
    if "comedy" == playFor(perf)['type']:
        result += perf['audience'] // 5
 
    return result
```

포인트 계산하는 부분을 함수로 추출해줍니다. 

## format 함수로 추출하기 

```pytthon
def mFormat(n):
    return format(n, ',.2f')
```

금액을 출력하는 부분에 사용된는 format 를 함수로 추출해줍니다.

## statement 함수에서 volumeCredits 제거하기 

```python
def volumeCreditsFor(perf):
    result = 0
 
    result += max(perf['audience'] - 30, 0)
    # 희극 관객 5명마다 추가 포인트를 제공한다.
    if "comedy" == playFor(perf)['type']:
        result += perf['audience'] // 5
 
    return result
```

총 포인트를 계산하는 함수를 따로 추출해줍니다.

## totalAmount 함수 추출 

```pyhon
def totalAmount(invoice):
    result = 0
    for perf in invoice['performances']:
        result += amountFor(perf)
 
    return result
```

totalAmount 부분도 함수로 추출해줍니다. 
