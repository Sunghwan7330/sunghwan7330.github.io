---
title: "[임베디드 C를 윈한 TDD] 13장 레거시 코드에 테스트 추가하기"
excerpt: "해당 내용은 '임베디드 C를 위한 TDD' 책의 13장 내용을 정리하였습니다. "

categories:
  - 'embedded_c_tdd'
tags:
  - 임베디드
  - TDD

toc: true
toc_sticky: true

date: 2022-08-09
last_modified_at: 2022-08-09
---

# 개요 

6장을 포스팅한 뒤 갑자기 13장을 포스팅하게 되었습니다. 

앞부분에서 TDD 가 무엇인지를 파악한 뒤 필요한 부분 부터 보려고 하다 보니까 13장을 우선적으로 보게 되었습니다. 


# 레거시 코드 변경 정책 

* 새로운 코드는 테스트 주도로 개발한다
* 레거시 코드를 수정하기 전에 테스트를 추가한다 
* 레거시 코드에서 변경되는 것들은 테스트 주도로 개발한다 

# 보이스카우트 원칙 

보이스카우트는 `캠프를 떠날 때는 처음 왔을 때보다 더 깨끗해야 한다` 라는 단순한 원칙을 따릅니다. 
즉, 수정한 부분에 대해서는 최대한 깔끔하게 코드를 깔끔하게 작성해야 한다는 의미입니다. 

## 긴 함수에 추가하기 

긴 함수는 따로 뽑아내어 이름을 붙일만한 내용이 많을 것입니다. 
예시로 긴 함수에 새로 세줄을 추가하고자 한다면 다섯줄을 추출하도록 해보겠습니다. 
결과적으로 이 함수는 두줄이 짧아집니다. 

단, 기존 동작을 보존하기 위한 테스트는 먼저 추가되어야 합니다. 

## 복잡한 조건식에 추가하기 

조건식을 추출하여 이름을 잘 붙인 도움 함수로 만드는 방법입니다. 

새로 추가한 코드의 조건식이 다른 모듈에도 포함되는 경우가 많은데 이는 함수로 이동해야 마땅합니다. 

## 암호 같은 지역변수 이름

일단 변수의 목적을 이해했다면, 다음에 이 코드를 보게 될 여러분과 팀원들을 위해 이름을 변경하는 것이 좋습니다. 

## 과도한 중첩 

단계 깊이의 중첩을 도움 함수로 변경하는 것이 좋습니다. 
조건문이 중첩되어 있으면 보호절 형태로 바꾸어 중첩의 깊이를 낮추는 것이 좋습니다. 

# 레거시 코드 변경 알고리즘 

마이클은 레거시 코드 변경 알고리즘을 아래와 같이 정의한다.

1. 변경점을 식별한다.
2. 테스트 포인트를 찾는다.
3. 의존성을 깨뜨린다.
4. 테스트를 작성한다.
5. 변경하고 리팩터링한다.

## 1. 변경점 식별

첫 단계는 현재 코드에서 변경이 필요하다고 생각하는 부분을 찾아야 합니다. 

## 2. 테스트 포인트 찾기 


변경점이 식별되었으면, 이것을 어떻게 테스트할지 고민해야 합니다. 
해당 코드가 어디서 입력을 받는지, 상황을 감시하기 위한 지점이 어디인지 살펴봐야 합니다. 

## 3. 의존성 깨뜨리기 (혹은 그러지 않기)

레거시 코드를 테스트 하니스에 붙이거나 일부 테스트 포인트에 접근하기 위해서는 의존성을 깨트려야 합니다. 

테스트 대역을 사용하려면 먼저 안전한 함수 추출 리팩터링을 적용할 필요가 있습니다. 
레거시 코드 수정시 이는 좀 더 주의할 필요가 있습니다. 
동료와 함께 작업하는 것이 실수를 방지하는 데에 도움이 될 것입니다. 

전역 데이터에 대한 의존성을 깨트리기 위해서는 문제의 전역 데이터를 접근할 때 접근 함수를 통하도록 캡슐화 할 수 있습니다. 
그런다음 테스트를 하는 동안 접근자를 오버라이드 하는 벙법으로 전역 데이터에 대한 통제권을 확보할 수 있습니다. 

## 4. 테스트 작성 

테스트 포인트를 찾았다면 레거시 코드의 현재 동작을 보존하면서 그 특징이 드러나는 테스트를 작성해야 합니다. 
이는 매우 까다로우며, 특히 테스트 하니스를 처음 연결한다면 더더욱 그럴 것입니다. 
이 부분은 아래 ‘부딪혀가며 통과하기’ 에서 다시 살펴보겠습니다. 

## 5. 변경하고 리팩터링

기존 동작을 보존해주는 테스트가 마련되었으면, 리팩터링을 적용할 수 있는 안전이 확보되었습니다. 
리팩터링을 통해 변경하기 쉽도록 코드를 재구성합니다. 
래거시 동작이 테스트를 통해 안정적으로 유지되면 새로운 동작을 TDD로 작성하기 수월해집니다. 

# 테스트 포인트

코드가 하는 일을 우리가 제대로 이해하고 있는지 확인하기 위해서는 테스트 포인
트가 필요합니다. 
테스트 포인트는 종류에 따라서 비교적 쉽게 확보할 수 있는 것들도 있습니다. 

## 봉합 

함수 호출은 코드의 다른 부분들 사이에 봉합(seam)을 형성합니다. 
이런 봉합이 테스트 포인트로는 좋습니다. 
봉합을 통해서 우리는 테스트 대상 코드가 하는 일을 살펴 보고 동작에 영향을 미칠 수 있습니다. 

봉합 지점에 테스트 대역을 투입하여 다른 모듈로 전달되는 데이터를 감시할 수 있습니다. 
함수 호출 봉합에서 테스트 대역이 값을 반환하는 방식으로 테스트 대상 코드에 간접 입력들 제공할 수도 있습니다. 


## 감지 변수

감지 변수는 접근이 어려운 데이터나 길이가 긴 함수에서 중간 결과를 확인하는데 유용합니다. 

리팩터링이 안된 매우 긴 함수가 있다고 가정해보겠습니다. 
여러분은 어디를 변경해야 할지 알고 있지만 구조적인 변경부터 시작하기에는 위험 부담이 있습니다. 
이런 경우 전역변수 형태의 감지 변수를 한두 개 추가하여 위험 부담을 줄일 수 있습니다. 
감지 변수는 길게 이어지는 계산 과정의 중간 값이나 상태 변수의 값을 조사하는데 사용하거나 반복문의 반복 횟수를 셀 때도 사용할 수 있습니다. 

테스트 목적으로 전역변수를 추가하는 아이디어에 대해 부정적일 수 있습니다. 
하지만 이러한 감지변수를 도입하는 것은 긴 함수를 풀어헤치는 중간 과정으로 생각하면 됩니다. 
만약 TDD로 코드를 개발하였다면, 감지 변수 대신 함수 봉합을 이용할 수 있을 것입니다. 


##디버그 출력 감지 포인트

개발 후 디버깅을 하다보면 코드 여기저기에서 디버그를 출력 함수를 호출하는 경우가 많습니다. 
디버그 출력은 보통 옵션을 통해 켜고 끌 수 있습니다. 
이러한 디버그 출력 장치를 ‘디버그 출력 감지 포인트’ 로 이용하여 접근이 쉽지 않은 코드의 동작에 대한 정보를 얻을 수 있습니다. 
이러한 방법으로 디버그 출력 대신 테스트를 통해 모니터링을 할 수 있습니다. 


# 테스트 주도로 버그 수정하기

버그를 수정할 때에도 테스트가 필요합니다.

만약 버그를 수정하면서 단위테스트를 추가할 수 있다면 추가하는 것이 좋습니다. 
버그를 수정하면서 다른 버그를 만들지 않고 싶을 것입니다. 
그렇기 떄문에 올바른 동작을 고정시키기 위해 테스트를 작성해야 하는 것을 의미합니다. 

버그를 잡으면서 테스트를 추가하는데 드는 비용이 늘엇을 때 앞으로 생기는 이슈에 대한 비용이 감소한다고 생각하면 좋습니다. 
결함을 제대로 수정하였을 때 추가되는 비용은 많지 않습니다. 
