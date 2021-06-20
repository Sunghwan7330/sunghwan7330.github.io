---
title: "[C코드 최적화] loop 돌리는 요령"
excerpt: "해당 내용은 '임베디드 프로그래밍 C코드 최적화' 책의 story 17에 내용을 정리하였습니다. "

categories:
  - 'embedded_c_optimization'
tags:
  - c코드 최적화
  - 북스터디
  - 최적화
  - 루프최적화
  - for문

toc: true
toc_sticky: true

date: 2021-06-20
last_modified_at: 2021-06-20
---

# 개요 

해당 내용은 '임베디드 프로그래밍 C코드 최적화' 책의 story 17에 내용을 정리하였습니다.

반복문 사용시 성능을 고려하여 작성하는 방법을 알아보려 합니다. 

# 루프 풀기 (Loop unroolling)

```c
void test1() {
  int i, a[100];
  for(i=0; i<100; i++) {
    a[i] = i + 1;
  }
}

void test2() {
  int i, a[100];
  for(i=0; i<100; i += 4) {
    a[i] = i + 1;
	a[i+1] = i + 2;
	a[i+2] = i + 3;
	a[i+3] = i + 4;
  }
}
```

test1과 test2는 같은 동작을 하는 함수입니다. 
하지만 실행 속도를 보면 test2가 조금 더 빠르게 동작합니다. 

for문은 루프를 반복할 때 마다 비교 연산 수행, 반복을 위한 계산을 수행합니다. 

test1 함수는 비교연산 수행, 반복을 위한 계산을 100회 수행하는 반면 test2는 25회만 수행합니다. 
따라서 test2 가 속도면에서는 더 빠르게 동작합니다. 


하드코딩하는 횟수를 늘리고 반복횟수를 줄이면 속도는 증가하겠지만, 코드가 지저분해지고 메모리 사용량이 늘어날 것입니다. 
따라서 상황에 따라 적절히 루프를 풀어주는것이 좋아보입니다. 
(요즘같은 컴퓨팅 환경이라면 최적화가 필요한 상황에서만 해주는것이 좋을듯합니다)

# 루프 병합 (Loop fusion)

```c
void test1() {
  int a[10], i, sum = 0;
  for(i = 0; i < 10; i++)
    a[i] = i+1;
	
  for (i 0;i < 10; i++)
    sum += a[ i ];
}

void test2() {
  int a[10], i, sum = 0;
  for (i = 0; i < 10; i++){
    a[i] + 1;
    sum += a[i];
  }
}

```

루프 병합은 근접한 루프를 하나로 합지는 방법으로 최적화를 합니다. 
위의 루프 풀기에서 언급한바와 같이 test1은 for문을 2회 반복함으로써 비교연산, 반복을 위한 계산을 더 많이 하게 됩니다. 
합칠 수 있는 루프는 최대한 합치는 것이 좋습니다. 

# 루프와 상관 없는 계산은 루프 밖으로!

```c
// before
for (i = 0; i < 30; i++){
  sum[i] = groupl->classA->person[i]->kor + 
           groupl->classA->person[i]->math +
		   group1->classA->person[i]->eng;
}

//after
struct student *tmp = groupl->classA;
for (i = 0; i < 30; i++){
  sum[i] = tmp->person[i]->kor +
           tmp->person[i]->math +
           tmp->person[i]->eng;
}
```

before 의 로직은 루프 안에서 `groupl->classA->person[i]` 를 매번 반복하고 있습니다. 
이는 포인터의 포인터의 포인터 접근을 매 루프마다 반복하기 때문에 좋지 않습니다. 

after 처럼 loop 밖에서 필요한 포인터 연산을 한 뒤 루프에 진입하는게 좋습니다. 

예제는 책에 있는걸 그대로 작성하였는데....

loop 안에서 `tmp->person[i]` 를 한번 계산한 뒤 `kor`, `math`, `eng`에 접근하는게 좋겠네요. 위의 코드는 sum 계산시 세번에 포인터 계산을 하기 때문입니다.


