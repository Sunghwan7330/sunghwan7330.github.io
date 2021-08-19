---
title: "문제 2 – 메뉴 리뉴얼"
excerpt: "2021 카카오 신입공채 1차 온라인 코딩 테스트 문제 2번 문제에 대한 풀이입니다."

categories:
  - '2021_kakao_coding_test'
tags:
  - algorithm
  - 코딩테스트

date: 2021-08-12
last_modified_at: 2021-08-12
---

# 개요 

2021 카카오 신입공채 1차 온라인 코딩 테스트 문제를 하나씩 풀고 풀이를 올려보려 합니다. 
제 방식이 정답은 아니고 더 좋은 방법도 있을껍니다. 
풀이법은 참고용으로 봐주세요. 
더 좋은 방법이 있으면 조언도 부탁드립니다. 

2번 문제 시작하겠습니다. 


# 문제 2 - 메뉴 리뉴얼

##문제 설명

레스토랑을 운영하던 스카피는 코로나19로 인한 불경기를 극복하고자 메뉴를 새로 구성하려고 고민하고 있습니다.
기존에는 단품으로만 제공하던 메뉴를 조합해서 코스요리 형태로 재구성해서 새로운 메뉴를 제공하기로 결정했습니다. 어떤 단품메뉴들을 조합해서 코스요리 메뉴로 구성하면 좋을 지 고민하던 "스카피"는 이전에 각 손님들이 주문할 때 가장 많이 함께 주문한 단품메뉴들을 코스요리 메뉴로 구성하기로 했습니다.
단, 코스요리 메뉴는 최소 2가지 이상의 단품메뉴로 구성하려고 합니다. 또한, 최소 2명 이상의 손님으로부터 주문된 단품메뉴 조합에 대해서만 코스요리 메뉴 후보에 포함하기로 했습니다.

예를 들어, 손님 6명이 주문한 단품메뉴들의 조합이 다음과 같다면,
(각 손님은 단품메뉴를 2개 이상 주문해야 하며, 각 단품메뉴는 A ~ Z의 알파벳 대문자로 표기합니다.)

| 손님 번호 | 주문한 단품메뉴 조합 |
| --- | --- |
|1번 손님 | A, B, C, F, G|
| 2번 손님 | A, C |
| 3번 손님 | C, D, E |
| 4번 손님 | A, C, D, E |
| 5번 손님 | B, C, F, G |
| 6번 손님 | A, C, D, E, H |

가장 많이 함께 주문된 단품메뉴 조합에 따라 "스카피"가 만들게 될 코스요리 메뉴 구성 후보는 다음과 같습니다.

| 코스 종류 | 메뉴 구성 | 설명 |
| --- | --- | --- |
| 요리 2개 코스 | A, C | 1번, 2번, 4번, 6번 손님으로부터 총 4번 주문됐습니다. |
| 요리 3개 코스 | C, D, E | 3번, 4번, 6번 손님으로부터 총 3번 주문됐습니다. |
| 요리 4개 코스 | B, C, F, G | 1번, 5번 손님으로부터 총 2번 주문됐습니다. |
| 요리 4개 코스 | A, C, D, E | 4번, 6번 손님으로부터 총 2번 주문됐습니다. |

## [문제]

각 손님들이 주문한 단품메뉴들이 문자열 형식으로 담긴 배열 orders, "스카피"가 추가하고 싶어하는 코스요리를 구성하는 단품메뉴들의 갯수가 담긴 배열 course가 매개변수로 주어질 때, "스카피"가 새로 추가하게 될 코스요리의 메뉴 구성을 문자열 형태로 배열에 담아 return 하도록 solution 함수를 완성해 주세요.

## [제한사항]

orders 배열의 크기는 2 이상 20 이하입니다.
orders 배열의 각 원소는 크기가 2 이상 10 이하인 문자열입니다.
각 문자열은 알파벳 대문자로만 이루어져 있습니다.
각 문자열에는 같은 알파벳이 중복해서 들어있지 않습니다.
course 배열의 크기는 1 이상 10 이하입니다.
course 배열의 각 원소는 2 이상 10 이하인 자연수가 오름차순으로 정렬되어 있습니다.
course 배열에는 같은 값이 중복해서 들어있지 않습니다.
정답은 각 코스요리 메뉴의 구성을 문자열 형식으로 배열에 담아 사전 순으로 오름차순 정렬해서 return 해주세요.
배열의 각 원소에 저장된 문자열 또한 알파벳 오름차순으로 정렬되어야 합니다.
만약 가장 많이 함께 주문된 메뉴 구성이 여러 개라면, 모두 배열에 담아 return 하면 됩니다.
orders와 course 매개변수는 return 하는 배열의 길이가 1 이상이 되도록 주어집니다.

## [입출력 예]

| orders | course | result |
| --- | --- | --- |
| ["ABCFG", "AC", "CDE", "ACDE", "BCFG", "ACDEH"] | [2,3,4] | ["AC", "ACDE", "BCFG", "CDE"] |
| ["ABCDE", "AB", "CD", "ADE", "XYZ", "XYZ", "ACD"] | [2,3,5] | ["ACD", "AD", "ADE", "CD", "XYZ"] |
| ["XYZ", "XWY", "WXA"] | [2,3,4] | ["WX", "XY"] |

## 입출력 예에 대한 설명

### 입출력 예 #1

문제의 예시와 같습니다.

### 입출력 예 #2

AD가 세 번, CD가 세 번, ACD가 두 번, ADE가 두 번, XYZ 가 두 번 주문됐습니다.
요리 5개를 주문한 손님이 1명 있지만, 최소 2명 이상의 손님에게서 주문된 구성만 코스요리 후보에 들어가므로, 요리 5개로 구성된 코스요리는 새로 추가하지 않습니다.

### 입출력 예 #3

WX가 두 번, XY가 두 번 주문됐습니다.
3명의 손님 모두 단품메뉴를 3개씩 주문했지만, 최소 2명 이상의 손님에게서 주문된 구성만 코스요리 후보에 들어가므로, 요리 3개로 구성된 코스요리는 새로 추가하지 않습니다.
또, 단품메뉴를 4개 이상 주문한 손님은 없으므로, 요리 4개로 구성된 코스요리 또한 새로 추가하지 않습니다.

# 소스코드 

```python 
check_arr = []
course_dic = {}
def search_dfs(order, course, depth, size):
    if depth > size : return

    #print (course)

    global course_dic
    if len(course) >= 2:
        if not course in course_dic:
            course_dic[course] = 1
        else:
            course_dic[course] += 1

    global check_arr
    for i in range(0, size):
        if check_arr[i] == 0 :
            if course == '' or order[i] > course[-1]:
                check_arr[i] = 1
                next_course = course + order[i]
                search_dfs(order, next_course, depth+1, size)
                check_arr[i] = 0
    return

def process_course(order):
    size = len(order)

    global check_arr
    check_arr = [0 for i in range(size)]

    search_dfs(order, '', 0, size)

    return

def solution(orders, course):
    answer = []

    global course_dic
    course_dic = {}

    for order in orders:
        process_course(order)

    max_cource_arr = [0 for i in range(11)]
    for key, value in course_dic.items():
        if value >= 2:
            key_len = len(key)
            if key_len in course and max_cource_arr[key_len] < value:
                max_cource_arr[key_len] = value

    for key, value in course_dic.items():
        if value >= 2:
            key_len = len(key)
            if value == max_cource_arr[key_len]:
                answer.append(key)

    answer.sort()
    #print(answer)
    return answer

def main():
    testcases = [
        [["ABCFG", "AC", "CDE", "ACDE", "BCFG", "ACDEH"], [2,3,4], ["AC", "ACDE", "BCFG", "CDE"]],
        [["ABCDE", "AB", "CD", "ADE", "XYZ", "XYZ", "ACD"], [2, 3, 5], ["ACD", "AD", "ADE", "CD", "XYZ"]],
        [["XYZ", "XWY", "WXA"], [2, 3, 4], ["WX", "XY"]]
    ]

    num = 1
    for tc in testcases:
        print("* %d." % num)
        ret = solution(tc[0],tc[1])
        res = 1
        for i in range(len(tc[2])):
            if not ret[i] == tc[2][i]:
                res = 0
                break;
        if res == 1:
            print("  - success")
        else:
            print("  - fail")
        num += 1

if __name__ == '__main__':
    main()
```


# 소스코드 풀이 

전체적인 로직의 흐름을 작성한 solution 함수에 대해 살펴보겠습니다. 

```python
    for order in orders:
        process_course(order)
```
저는 손님이 주문한 메뉴들을 process_course 함수에서 모든 경우의 수를 찾도록 하였습니다. 
DFS를 이용하여 완전탐색하고, 메뉴 두개 이상 조합되는 모든 메뉴를 딕셔너리에 넣었습니다. 

만약 ABCD 를 주문한 손님이 있다면 모든 경우의 수는 아래와 같습니다. 

* AB
* AC
* AD
* BC
* BD
* CD
* ABC
* ABD
* BCD
* ABCD

위의 경우의 수를 딕셔너리의 key로 하고, value는 1씩 증가하도록 하였습니다. 
이렇게 모든 사람의 메뉴에 대해 모든 경우의 수를 탐색하여 딕셔너리에 넣게 되면 모든 사람들의 모든 메뉴 경우의 수의 합산이 입력되게 됩니다. 


```python
    max_cource_arr = [0 for i in range(11)]
    for key, value in course_dic.items():
        if value >= 2:
            key_len = len(key)
            if key_len in course and max_cource_arr[key_len] < value:
                max_cource_arr[key_len] = value
```
다음은 `course`의 최대값을 구하는 로직입니다. 
`course`가 `[2, 3]` 라면, 메뉴 2개 조합 중 가장 많은수와 메뉴 3개 조합 중 가장 많은 수를 코스로 만들어야 합니다. 
`max_cource_arr`는 그 최대치를 저장하기 위한 배열입니다. 

이후 `course_dic` 의 모든 item을 확인하여 각 코스에서 가장 많이 주문한 수를 `max_cource_arr`에 저장하게 됩니다. 
(지금 생각해보니 `max_cource_arr`는 `course` 와 같은 크기로 해도 됫네요)

위 로직을 수행하게 되면 사람들이 주문한 메뉴의 조합으로 각 코스에서 많이 겹치는 최대값을 알 수 있습니다. 
만약 `max_cource_arr[2]` 의 값이 3 이라면, 메뉴 2개 조합의 코스는 3명의 사람과 겹친다는 것을 알 수 있습니다. 

```python
    for key, value in course_dic.items():
        if value >= 2:
            key_len = len(key)
            if value == max_cource_arr[key_len]:
                answer.append(key)
	answer.sort()
```
마지막으로 정답을 구하는 부분입니다. 
`course_dic` 의 모든 item을 확인하여 `max_cource_arr` 에 저장된 최대값과 같은 코스를 `answer`에 저장합니다. 
이후 `sort()`함수를 통해 정답을 정렬해주면 됩니다. 

# 마무리 

처음 생각한대로 코드를 짜고 제출하여 통과하였습니다. 
그리고 포스팅을 작성하는데 뭔가 아쉬운 부분이 조금씩 보이네요. 

혹시나 이 포스팅을 참고하시는 분들은 좀 더 개선된 코드를 작성해보시기 바랍니다. :)


# 참고 

* https://tech.kakao.com/2021/01/25/2021-kakao-recruitment-round-1/
