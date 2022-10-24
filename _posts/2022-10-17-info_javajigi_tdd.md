---
title: "[우아한테크세미나] TDD 리팩토링 by 자바지기 박재성님 내용 정리"
excerpt: "유튜브 우아한Tech 체널에 공개된 세미나 중 박재성님의 TDD 강의 내용을 정리하였습니다."

categories:
  - 'infomation'
tags:
  - TDD
  - Refactoring

date: 2022-10-17
last_modified_at: 2022-10-17
---

# 개요 

최근 TDD에 대한 관심이 생겨 자료를 찾던 중 유튜브 우아한Tech 체널에 공개된 세미나 중 박재성님의 TDD 강의 내용을 정리하였습니다.


# 1. 의식적인 연습이란?

## TDD 리팩터링을 잘하려면 연습을 많이 할 것

무조건 연습을 많이 한다고 잘 할수는 없습니다. 
다만 많은 노력을 통해 숙련되어야 자연스럽고 능숙하게 TDD를 수행할 수 있습니다. 

강사님은 TDD를 5~6년을 도전한 후에야 테스트하기 쉬운 코드와 테스트하기 어려운 코드를 보는 눈이 생기고, 테스트하기 어려운 코드를 테스트하기 쉬운 코드로 설계하는 감이 생긴다고 말합니다. 

TDD 리팩터링은 멋져보이지만 생각만큼 연습하기 어려운 점이 있습니다. 

강사님이 교육자로써의 좀 더 효과적으로 쉽게 연습할 수 있는 방법을 고민하고 계신다고 합니다. 

TDD를 포함하여 무언가에 노력하기 위한 서적으로 아래의 서적을 추천하셨습니다. 

[그림]

위의 책에 소개된 의식적인 연습의 7가지 원칙은 아래와 같습니다. 

1. 효과적인 훈련 기법이 수립되어 있는 기술 연마 
2. 개인의 컴포트 존을 벗어난 지점에서 진행,  자신의 현재 능력을 살짝 넘어가는 작업을 지속적으로 시도 
3. 명확하고 구체적인 목표를 가지고 진행 
4. 신중하고 계획적. 즉, 개인이 온전히 집중하고 ‘의식적’으로 행동할 것을 요구 
5. 피드백과 피드백에 따른 행동 변경을 수반
6. 효과적인 심적 표상을 만들어내는 한편 심적 표상에 의존
7. 기존에 습득한 기술의 특정 부분을 집중적으로 개선함으로써 발전시키고 수정하는 과정을 수반

## 의식적인 연습 예시 - 우테코 프리코스 

강사님은 `우아한 테크캠프` 라는 자바 강의를 하고 계십니다. 
해당 강의는 인원을 선발하여 진행하는데, 프리코스를 통해 인원을 선발합니다. 

* 3주동안 진행 매주 해결해야 할 미션을 부여 
* 미션을 완료한 후 github의 pull request로 제출
* 각 PR 평가 후 공통 피드백 제공 

선발 과정이지만 3주 동안 배움이 있는 과정을 만드는 것을 목표로 하고 있습니다. 
단순 평가가 아닌 참여간 효과적인 피드백이 갈 수 있도록 합니다. 

1주마다 새로운 제약사항을 추가하여 개발을 진행하도록 하는데, 주어지는 제약사항은 아래와 같습니다. 

### 1주차 - 프로그래밍 제약사항

* 자바 코드 컨벤션을 지키면서 코딩함 
* 인덴트 뎁스 3이 넘지 않도록 구현 
* 함수 또는 메서드는 한가지 일만 하도록 작게 구현 

### 2주차 - 프로그래밍 제약 

* 함수 또는 메서드의 길이가 15라인을 넘어가지 않도록 구현 
  * 함수는 한가지 일만 잘 하도록 구현 
* else 예약어를 쓰지 않는다 

### 3주차 

* 함수의 길이가 10라인을 넘어기자 않도록 구현 
* 인덴트 뎁스 2 넘지 않도록 구현 
* 함수의 인자 수를 3개까지만 허용함

이와 같은 훈련을 하였을 때 코드는 더욱 간결해지고 가독성이 좋아집니다. 

[그림]

## 의식적인 연습으로 TDD, 리팩토링 적용 - 개인 

TDD, 리팩터링은 꾸준한 운동과 같다고 합니다. 
평소에 30분 ~ 1시간씩 꾸준히 운동하는 것 처럼 평생동안 연습하겠다는 마음가짐으로 연습하는 것이 좋다고 합니다. 

이를 위해서는 꾸준히 연습할 시간을 확보하는 것이 중요할 것입니다. 

연습할 시간을 확보하였으면 단계적으로 연습을 하도록 합니다. 

### 1단계 - 단위테스트 연습 

내가 사용하는 API 사용법을 익히기 위한 학습 테스트에서 시작하는 것이 좋습니다. 

* 자바 String 클래스의 다양한 메소드 사용법
* 자바 ArrayList 에 데이터를 추가, 수정, 삭제하는 방법 

[그림]

연습의 효과는 다음과 같습니다. 

* 단위 테스트 방법을 학습할 수 있음 
* 단위테스트 도구 학습법을 익힐 수 있음 
* 사용하는 API 에 대한 학습 효과가 있음 

### 2단계 - TDD 연습 

어려운 문제를 해결하는 것이 목적이 아니라 TDD를 연습하는 것을 목적으로 가벼운 프로젝트를 진행하는 것이 좋습니다. 

난이도가 낮거나 자신에게 익숙한 문제로 시작하는 것을 추천합니다. 
웹, 모바일 UI, DB 의존관계를 가지지 않는 요구사항으로 연습하는 것이 좋습니다. 

#### 예시 : 문자열 덧셈 계산기 요구사항 

* 요구사항 : 쉼표(,) 또는 콜론(:) 을 구분자로 가지는 문자열을 전달하는 경우 구분자를 기준으로 분리한 각 숫자의 합을 반환

| 입력 | 출력 | 
| --- | --- |
| null 또는 “” | 0 |
| “1” | 1 |
| “1, 2” | 3 |
| “1,2:3” | 6 |

* TDD 사이클

[그림]

* `실패하는 테스트 생성 -> 테스트 통과하는 코드 작성 -> 코드 리팩터링 -> 실패하는 테스트 작성 -> …..`
* `실패하는 테스트 생성 -> 테스트 통과하는 코드 작성`  만 반복하는 것은 TDD가 아님
* TDD의 핵심은 리팩터링임


* 테스트 코드 작성 
  * 원래는 테스트도 하나씩 추가하고 구현해야 함 (강의 편의상 한번에 진행)
  
```java
public class StringCalculatorTest {
    @Test
	public void null_또는_빈값() {
	    assertThat(StringCalculator.splitAndSum(null)).isEqualTo(0);
		assertThat(StringCalculator.splitAndSum("")).isEqualTo(0);
	}
	
	@Test
	public void 값_하나() {
	    assertThat(StringCalculator.splitAndSum("1")).isEqualTo(1);
	}
	
	@Test
	public void 쉼표_구분자() {
	    assertThat(StringCalculator.splitAndSum("1,2")).isEqualTo(3);
	}
	
	@Test
	public void 쉼표_콜론_구분자() {
	    assertThat(StringCalculator.splitAndSum("1,2:3")).isEqualTo(6);
	}
}
```



#### 메소드 분리 리팩터링 

* 리팩터링 할 것들 
  *  else 제거, 인덴트 줄이기 등등...

* TDD 가 처음에는 막막하고 답답할 수 있지만, 꾸준한 연습을 통해 코드를 보는 눈을 기르는 것이 중요함 

* 테스트 코드를 건들이지 않고 위 함수에 대해서만 리팩터링 진행 

```java
public class StringCalculator {
    public static int splitAddSum (String text) {
	    int result = 0;
		if (text == null || text.isEmpty()) {
		     result = 0;
		}
		else {
		    String[] values = text.split(",|:");
			for (String value : values) {
			    result += Integer.ParseInt(value);
			}
		}
		return result;
	}
}
```

* 메서드 분리를 통한 인덴트 줄이기 

```java
public class StringCalculator {
    public static int splitAddSum (String text) {
	    int result = 0;
		if (text == null || text.isEmpty()) {
		     result = 0;
		}
		else {
		    String[] values = text.split(",|:");
			result = sum(values);
		}
		return result;
	}
	
	private static int sum(String[] vaules) {
		int result = 0;
	    for (String value : values) {
	        result += Integer.ParseInt(value);
	    }
		return result;
	}
}
```

* else 예약어 제거하기 

```java
public class StringCalculator {
    public static int splitAddSum (String text) {
	    int result = 0;
		if (text == null || text.isEmpty()) {
		     return 0;
		}
		
		String[] values = text.split(",|:");
		return sum(values);
	}
	
	private static int sum(String[] vaules) {
		int result = 0;
	    for (String value : values) {
	        result += Integer.ParseInt(value);
	    }
		return result;
	}
}
```

* 메서드가 한 가지 일만 하도록 구현하기 
  * sum 함수가 string을 int로 변경과 덧셈 역할을 하고 있음
  * 이 역할을 각각의 메서드로 분리
  * 반복문을 두번 돌지만 값이 크지 않다면 성능적인 영향은 적음 
  * 메서드를 분리함으로써 메서드의 재사용성이 증가함
  
```java
public class StringCalculator {
    public static int splitAddSum (String text) {
	    int result = 0;
		if (text == null || text.isEmpty()) {
		     return 0;
		}
		
		String[] values = text.split(",|:");
		return sum(toInts(values));
	}
	
    private static int[] toInts(Strings[] values) {
	    int[] numbers = new Int[values.length];
		for (int i=0; i<values.length; i++) {
		    numbers[i] = Integer.parseInt(values[i]);
		}
		return numbers;
	}
	
	private static int sum(String[] numbers) {
		int result = 0;
	    for (int number : numbers) {
	        result += number;
	    }
		return result;
	}
}
```

* 로컬 변수가 필요 없다면 합치기 

```java
public class StringCalculator {
    public static int splitAddSum (String text) {
	    int result = 0;
		if (text == null || text.isEmpty()) {
		     return 0;
		}
		
		return sum(toInts(text.split(",|:")));
	}
	
    private static int[] toInts(Strings[] values) {
	    [...]
	}
	
	private static int sum(String[] numbers) {
		[...]
}
```

* compose method 패턴 적용 
  * 어떤 메서드의 내부 로직이 한 눈에 이해하기 어렵다면, 그 로직을 의도가 잘 드러나며 동등한 수준의 작업을 하는 여러 단계로 나눔

```java
public class StringCalculator {
    public static int splitAddSum (String text) {
	    if (isBlank(text) 
		    return 0;
		
		return sum(toInts(split()));
	}
	
	private static boolean isBlank (String text) {
	    return (text == null || text.isEmpty())
	}
	
	private static String[] split(String text) {
	    return text.split(",|:")
	}
	
    private static int[] toInts(Strings[] values) {
	    [...]
	}
	
	private static int sum(String[] numbers) {
		[...]
}
```

* splitAddSum 함수를 처음 읽는 사람의 경우 어떤 코드가 더 읽기 좋을지 생각해볼 것

#### 리팩토링 연습 - 클래스 분리



# 2. TDD 리팩터링 적용 - 개인 

# 3. 리팩터링 적용 - 개인(주니어) -> 팀

# 4. TDD 리팩터링 적용 - 내가 리더 
