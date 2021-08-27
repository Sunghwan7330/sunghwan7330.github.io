---
title: "문제 3 – 순위 검색"
excerpt: "2021 카카오 신입공채 1차 온라인 코딩 테스트 문제 3번 문제에 대한 풀이입니다."

categories:
  - '2021_kakao_coding_test'
tags:
  - algorithm
  - 코딩테스트

date: 2021-08-22
last_modified_at: 2021-08-22
---

# 개요 

2021 카카오 신입공채 1차 온라인 코딩 테스트 문제를 하나씩 풀고 풀이를 올려보려 합니다. 
제 방식이 정답은 아니고 더 좋은 방법도 있을껍니다. 
풀이법은 참고용으로 봐주세요. 
더 좋은 방법이 있으면 조언도 부탁드립니다. 

3번 문제 시작하겠습니다. 


# 03 순위 검색

## 문제 설명

[본 문제는 정확성과 효율성 테스트 각각 점수가 있는 문제입니다.]

카카오는 하반기 경력 개발자 공개채용을 진행 중에 있으며 현재 지원서 접수와 코딩테스트가 종료되었습니다. 이번 채용에서 지원자는 지원서 작성 시 아래와 같이 4가지 항목을 반드시 선택하도록 하였습니다.

* 코딩테스트 참여 개발언어 항목에 cpp, java, python 중 하나를 선택해야 합니다.
* 지원 직군 항목에 backend와 frontend 중 하나를 선택해야 합니다.
* 지원 경력구분 항목에 junior와 senior 중 하나를 선택해야 합니다.
* 선호하는 소울푸드로 chicken과 pizza 중 하나를 선택해야 합니다.

인재영입팀에 근무하고 있는 `니니즈`는 코딩테스트 결과를 분석하여 채용에 참여한 개발팀들에 제공하기 위해 지원자들의 지원 조건을 선택하면 해당 조건에 맞는 지원자가 몇 명인 지 쉽게 알 수 있는 도구를 만들고 있습니다.
예를 들어, 개발팀에서 궁금해하는 문의사항은 다음과 같은 형태가 될 수 있습니다.

`코딩테스트에 java로 참여했으며, backend 직군을 선택했고, junior 경력이면서, 소울푸드로 pizza를 선택한 사람 중 코딩테스트 점수를 50점 이상 받은 지원자는 몇 명인가?`

물론 이 외에도 각 개발팀의 상황에 따라 아래와 같이 다양한 형태의 문의가 있을 수 있습니다.

* 코딩테스트에 python으로 참여했으며, frontend 직군을 선택했고, senior 경력이면서, 소울푸드로 chicken을 선택한 사람 중 코딩테스트 점수를 100점 이상 받은 사람은 모두 몇 명인가?
* 코딩테스트에 cpp로 참여했으며, senior 경력이면서, 소울푸드로 pizza를 선택한 사람 중 코딩테스트 점수를 100점 이상 받은 사람은 모두 몇 명인가?
* backend 직군을 선택했고, senior 경력이면서 코딩테스트 점수를 200점 이상 받은 사람은 모두 몇 명인가?
* 소울푸드로 chicken을 선택한 사람 중 코딩테스트 점수를 250점 이상 받은 사람은 모두 몇 명인가?
* 코딩테스트 점수를 150점 이상 받은 사람은 모두 몇 명인가? 

즉, 개발팀에서 궁금해하는 내용은 다음과 같은 형태를 갖습니다.

`* [조건]을 만족하는 사람 중 코딩테스트 점수를 X점 이상 받은 사람은 모두 몇 명인가?`

## [문제]

지원자가 지원서에 입력한 4가지의 정보와 획득한 코딩테스트 점수를 하나의 문자열로 구성한 값의 배열 info, 개발팀이 궁금해하는 문의조건이 문자열 형태로 담긴 배열 query가 매개변수로 주어질 때,
각 문의조건에 해당하는 사람들의 숫자를 순서대로 배열에 담아 return 하도록 solution 함수를 완성해 주세요.

## [제한사항]

* info 배열의 크기는 1 이상 50,000 이하입니다.
* info 배열 각 원소의 값은 지원자가 지원서에 입력한 4가지 값과 코딩테스트 점수를 합친 "개발언어 직군 경력 소울푸드 점수" 형식입니다.
  * 개발언어는 cpp, java, python 중 하나입니다.
  * 직군은 backend, frontend 중 하나입니다.
  * 경력은 junior, senior 중 하나입니다.
  * 소울푸드는 chicken, pizza 중 하나입니다.
  * 점수는 코딩테스트 점수를 의미하며, 1 이상 100,000 이하인 자연수입니다.
  * 각 단어는 공백문자(스페이스 바) 하나로 구분되어 있습니다.
* query 배열의 크기는 1 이상 100,000 이하입니다.
* query의 각 문자열은 "[조건] X" 형식입니다.
  * [조건]은 "개발언어 and 직군 and 경력 and 소울푸드" 형식의 문자열입니다.
  * 언어는 cpp, java, python, - 중 하나입니다.
  * 직군은 backend, frontend, - 중 하나입니다.
  * 경력은 junior, senior, - 중 하나입니다.
  * 소울푸드는 chicken, pizza, - 중 하나입니다.
  * '-' 표시는 해당 조건을 고려하지 않겠다는 의미입니다.
  * X는 코딩테스트 점수를 의미하며 조건을 만족하는 사람 중 X점 이상 받은 사람은 모두 몇 명인 지를 의미합니다.
  * 각 단어는 공백문자(스페이스 바) 하나로 구분되어 있습니다.
  * 예를 들면, "cpp and - and senior and pizza 500"은 "cpp로 코딩테스트를 봤으며, 경력은 senior 이면서 소울푸드로 pizza를 선택한 지원자 중 코딩테스트 점수를 500점 이상 받은 사람은 모두 몇 명인가?"를 의미합니다.

## [입출력 예]

| info | query | result|
|---|---|---|
| ["java backend junior pizza 150","python frontend senior chicken 210","python frontend senior chicken 150","cpp backend senior pizza 260","java backend junior chicken 80","python backend senior chicken 50"] | ["java and backend and junior and pizza 100","python and frontend and senior and chicken 200","cpp and - and senior and pizza 250","- and backend and senior and - 150","- and - and - and chicken 100","- and - and - and - 150"] | [1,1,1,1,2,4] |

## 입출력 예에 대한 설명

지원자 정보를 표로 나타내면 다음과 같습니다.

| 언어 | 직군 | 경력 | 소울 푸드 | 점수 |
|---|---|---|---|---|
| java | backend | junior | pizza | 150 |
| python | frontend | senior | chicken | 210 |
| python | frontend | senior | chicken | 150 |
| cpp | backend | senior | pizza | 260 |
| java | backend | junior | chicken | 80 |
| python | backend | senior | chicken | 50 |

* `"java and backend and junior and pizza 100"` : java로 코딩테스트를 봤으며, backend 직군을 선택했고 junior 경력이면서 소울푸드로 pizza를 선택한 지원자 중 코딩테스트 점수를 100점 이상 받은 지원자는 1명 입니다.
* `"python and frontend and senior and chicken 200"` : python으로 코딩테스트를 봤으며, frontend 직군을 선택했고, senior 경력이면서 소울 푸드로 chicken을 선택한 지원자 중 코딩테스트 점수를 200점 이상 받은 지원자는 1명 입니다.
* `"cpp and - and senior and pizza 250"` : cpp로 코딩테스트를 봤으며, senior 경력이면서 소울푸드로 pizza를 선택한 지원자 중 코딩테스트 점수를 250점 이상 받은 지원자는 1명 입니다.
* `"- and backend and senior and - 150"` : backend 직군을 선택했고, senior 경력인 지원자 중 코딩테스트 점수를 150점 이상 받은 지원자는 1명 입니다.
* `"- and - and - and chicken 100"` : 소울푸드로 chicken을 선택한 지원자 중 코딩테스트 점수를 100점 이상을 받은 지원자는 2명 입니다.
* `"- and - and - and - 150"` : 코딩테스트 점수를 150점 이상 받은 지원자는 4명 입니다.

# 1차시도 - 효율성 실패 

## 소스코드 

정답은 다 맞았지만 효율성에서 실패한 코드입니다. 

```python
info_dic = {
    "cpp" : 1,
    "java" : 2,
    "python" : 4,
    "backend" : 8,
    "frontend" : 16,
    "junior" : 32,
    "senior" : 64,
    "chicken" : 128,
    "pizza" : 256
}
DEV_LANG_LIST = [info_dic["cpp"], info_dic["java"], info_dic["python"]]
JOB_LIST = [info_dic["backend"], info_dic["frontend"]]
CAREER_LIST = [info_dic["junior"], info_dic["senior"]]
SOULFOOD_LIST = [info_dic["chicken"], info_dic["pizza"]]
LIST_ARR = [DEV_LANG_LIST, JOB_LIST, CAREER_LIST, SOULFOOD_LIST]
LIST_SUM_ARR = [sum(arr) for arr in LIST_ARR]


def convert_info_to_bit(info_arr):
    info_bit = 0
    for i in range(len(LIST_ARR)):
        if info_arr[i] == '-':
            info_bit += LIST_SUM_ARR[i]
        else:
            info_bit += info_dic[info_arr[i]]

    return info_bit


def decode_applicant_info(applicant_info):
    info_arr = applicant_info.split()
    info_bit = convert_info_to_bit(info_arr)

    return [info_bit, int(info_arr[-1])]

def solution(info, query_list):
    answer = []
    appl_info_arr = []

    for applicant_info in info:
        appl_info_arr.append(decode_applicant_info(applicant_info))
    #print(appl_info_arr)

    for query in query_list:
        query = query.replace(" and ", " ")
        items = query.split(" ")
        bit_val = convert_info_to_bit(items)
        score = int(items[-1])

        #print(query)
        #print(bit_val, score)

        total = 0
        for appl_info in appl_info_arr:
            if appl_info[0] & bit_val == appl_info[0] and  appl_info[1] >= score:
                total += 1
        answer.append(total)

    #print(answer)
    return answer

def main():
    testcases = [
        ["java backend junior pizza 150","python frontend senior chicken 210","python frontend senior chicken 150","cpp backend senior pizza 260","java backend junior chicken 80","python backend senior chicken 50"],
        ["java and backend and junior and pizza 100", "python and frontend and senior and chicken 200",
         "cpp and - and senior and pizza 250", "- and backend and senior and - 150", "- and - and - and chicken 100",
         "- and - and - and - 150"],
        [1, 1, 1, 1, 2, 4]
    ]

    num = 1
    print("* %d." % num)
    ret = solution(testcases[0],testcases[1])
    res = 1
    for i in range(len(testcases[2])):
        if not ret[i] == testcases[2][i]:
            res = 0
            break
    if res == 1:
        print("  - success")
    else:
        print("  - fail")
    num += 1

if __name__ == '__main__':
    main()
```

## 해설 

쿼리에 이용되는 각 item을 비트로 변경하여 비교하는 방식을 사용하였습니다. 

* cpp : 1,
* java : 2,
* python : 4,
* backend : 8,
* frontend : 16,
* junior : 32,
* senior : 64,
* chicken : 128,
* pizza : 256

만약 `cpp and backend and junior and pizza` 를 선택한 지원자라면, `1 + 8 + 32 + 256` 이 지원자의 key 가 됩니다. 
이를 바이너리로 표현한다면 `100101001` 이 되겠네요.

쿼리도 동일한 방식으로 바꿔줍니다. 이때 `-` 가 있다면 해당 type에 들어갈 수 있는 모든 값을 더합니다. 

만약 쿼리가 `- and backend and junior and pizza` 라면, `1 + 2 + 4 + 8 + 32 + 256` 이 됩니다.
이를 바이너리로 표현한다면 `100101111` 이 되겠네요.

지원자가 쿼리에 포함하는지를 확인하려면 이 둘을 and 연산을 하면 됩니다. 
지원자가 쿼리의 조건을 만족한다면 and 연산하였을 때 지원자의 key가 나옵니다. 
위에서 예시를 들었던 `cpp and backend and junior and pizza` 참가자는 `- and backend and junior and pizza` 쿼리를 만족합니다. 
이 둘의 key를 서로 and 해보도록 합시다. 

```
     100101001
and  100101111
---------------
     100101001
```

한번 더 해보겠습니다. 
`cpp and backend and junior and pizza` 참가자가 `- and - and - and -` 쿼리를 만족하는지 확인하기 위해 and 연산을 해봅시다.

```
     100101001
and  111111111
---------------
     100101001
```

and 연산을 하면 역시 지원자의 값과 같은 값이 나옵니다. 

쿼리를 만족하지 않으면 어떻게 될까요?
`cpp and backend and junior and pizza` 참가자가 `java and backend and junior and pizza` 쿼리를 만족하는지 확인하기 위해 and 연산을 해봅시다.

```
     100101001
and  100101010
---------------
     100101000
```

이제 확실히 차이가 보이실겁니다. 
지원자의 값과 쿼리의 값은 뒷부분의 비트만 다르게 됩니다. 
그렇기때문에 이를 and 연산하면 0으로 계산되여 지원자의 값이랑 다르게 되는 것입니다. 
각각의 아이템을 bit로 계산하였기 때문에 이와같이 and 연산으로 필터링이 가능해집니다. 

하지만 이 코드는 효율성 검사에서 떨어지게 됩니다. 
이 코드의 단점은 지원자 많을때 한 쿼리에 대해서 모든 지원자를 전부 and 연산해줘야한다는 점입니다. 
지원자가 많으면 많을수록 쿼리 하나를 수행하는데 걸리는 시간은 점점 늘어나게 될 것입니다. 

# 2차시도 - 정확성 통과, 효율성 통과

## 소스코드

```python
import bisect
from itertools import combinations

def solution(info, query_list):
    answer = []
    appl_info_arr = []
    alllicant_info_dic = {}

    for applicant_info in info:
        info_arr = applicant_info.split()
        info_key_arr = info_arr[:-1]
        info_val = int(info_arr[-1])

        for i in range(5):
            for c in combinations(info_key_arr, i):
                key = ''.join(c)
                #print(key)
                if key in alllicant_info_dic:
                    alllicant_info_dic[key].append(info_val)
                else:
                    alllicant_info_dic[key] = [info_val]

    for key, arr in alllicant_info_dic.items():
        arr.sort()

    #print(alllicant_info_dic)

    for q in query_list:
        q = q.split(" ")
        score = int(q[-1])
        q = q[:-1]
        
        for i in range(3):
            q.remove("and")
        while "-" in q:
            q.remove("-")
            
        key = ''.join(q)
        #print(key)

        if key in alllicant_info_dic:
            arr = alllicant_info_dic[key]
            all_len = len(arr)
            index = bisect.bisect_left(arr, score, lo=0, hi=all_len)
            answer.append(all_len - index)
        else:
            answer.append(0)
        
    #print(answer)
    return answer

def main():
    testcases = [
        ["java backend junior pizza 150","python frontend senior chicken 210","python frontend senior chicken 150","cpp backend senior pizza 260","java backend junior chicken 80","python backend senior chicken 50"],
        ["java and backend and junior and pizza 100", "python and frontend and senior and chicken 200",
         "cpp and - and senior and pizza 250", "- and backend and senior and - 150", "- and - and - and chicken 100",
         "- and - and - and - 150"],
        [1, 1, 1, 1, 2, 4]
    ]

    num = 1
    print("* %d." % num)
    ret = solution(testcases[0],testcases[1])
    res = 1
    for i in range(len(testcases[2])):
        if not ret[i] == testcases[2][i]:
            res = 0
            break
    if res == 1:
        print("  - success")
    else:
        print("  - fail")
    num += 1

if __name__ == '__main__':
    main()
```

## 해설

먼저, 별도의 함수를 선언하지 않고 inline 화하였습니다. 
간단한 함수라고 하더라도 함수 호출시 스택 메모리에 필요한 정보를 올리고, return 시 이를 정리하는데 부하가 있습니다. 
그렇기때문에 제귀함수와 같이 함수를 많이 호출하는 로직의 경우 효율성이 좋지 않습니다. 

그럼 어떻게 효율성을 올릴 수 있을까요?
이에 대해서 많은 고민을 해보았습니다. 

위에 1차시도에서는 지원자가 많을 때 복잡도가 높아지는 문제가 있었습니다. 
이러한 문제를 해결하기 가장 좋은 방법은 hash map을 사용하는 것입니다. 

hash map 은 key와 value로 구성된 자료구조이며, key에 해당하는 value를 빠르게 찾을 수 있다는 장점이 있습니다. 
python 에서는 딜셔너리라는 이름으로 hash map 자료구조를 제공합니다. 

`cpp and backend and junior and pizza 150` 참가자가 있다고 합시다. 
key를 `cpp backend junior pizza` 로 하고, value를 `[150]`으로 딕셔너리에 등록합니다. 
이와 같은 방법으로 모든 참가자의 정보를 딕셔너리에 입력할 수 있습니다. 
만약 같은 조건을 가진 참가자가 있다면 키는 같게하며, value의 배열에 점수를 추가해주는 식으로 입력합니다. 
이렇게하면 쿼리가 입력되었을 때 해당 쿼리를 key로 변환하여 딕셔너리에 입력하면 해당 조건을 만족하는 참가자들의 점수를 바로 알 수 있게됩니다. 

하지만 위와같이 한다면 쿼리에서 `-`가 입력되었을 때 결과를 얻을 수 없습니다.  
`-` 를 처리하기 위해서는 어떤 방법을 사용해야할까요?

정보가 `cpp and backend and junior and pizza 150` 인 참가자가 있다고 합시다. 
이 참가자는 아래의 16가지 쿼리를 만족할 수 있습니다. 

* `- - - - -`
* `cpp - - - -`
* `- backend - -`
* `- -junior -`
* `- - - pizza`
* `cpp backend - -`
* `cpp - junior -`
* `cpp - - pizza`
* `- backend junior -`
* `- backend - pizza`
* `- - junior pizza`
* `cpp backend junior -`
* `cpp backend - pizza`
* `cpp - junior pizza`
* `- backend junior pizza`
* `cpp backend junior pizza`

그러면 위의 모든 정보를 key로 입력하면 `-`에 대한 쿼리를 만족할 수 있습니다. 

이제 소스코드를 확인해보겠습니다. 

```python
    for applicant_info in info:
        info_arr = applicant_info.split()
        info_key_arr = info_arr[:-1]
        info_val = int(info_arr[-1])

        for i in range(5):
            for c in combinations(info_key_arr, i):
                key = ''.join(c)
                #print(key)
                if key in alllicant_info_dic:
                    alllicant_info_dic[key].append(info_val)
                else:
                    alllicant_info_dic[key] = [info_val]

    for key, arr in alllicant_info_dic.items():
        arr.sort()
```

먼저 `for`문을 통해 모든 참가자들의 정보를 반복합니다. 

이후 `combinations` 을 통해 참가자가 생성할 수 있는 모든 조합을 생성합니다. 
이때 `key = ''.join(c)` 으로 key를 생성합니다. 
즉 `-`은 그냥 공백으로 처리하여 키를 만들도록 하였습니다. 

이후 딕셔너리의 모든 value 에 있는 배열을 sorting합니다. 

이제 쿼리에 대한 결과를 찾으면 됩니다. 

```python
    for q in query_list:
        q = q.split(" ")
        score = int(q[-1])
        q = q[:-1]
        
        for i in range(3):
            q.remove("and")
        while "-" in q:
            q.remove("-")
            
        key = ''.join(q)
        #print(key)

        if key in alllicant_info_dic:
            arr = alllicant_info_dic[key]
            all_len = len(arr)
            index = bisect.bisect_left(arr, score, lo=0, hi=all_len)
            answer.append(all_len - index)
        else:
            answer.append(0)
        
    #print(answer)
    return answer
```

# 참고 

* https://tech.kakao.com/2021/01/25/2021-kakao-recruitment-round-1/
