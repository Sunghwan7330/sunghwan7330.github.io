---
title: "[임베디드 C를 윈한 TDD] 4장 완료까지 테스트하기"
excerpt: "해당 내용은 '임베디드 C를 위한 TDD' 책의 4장 내용을 정리하였습니다. "

categories:
  - 'embedded_c_tdd'
tags:
  - 임베디드
  - TDD

toc: true
toc_sticky: true

date: 2022-06-13
last_modified_at: 2022-06-13
---

# 개요 

본 장에서는 첫 예제인 LED 드라이버를 제작하면서 TDD를 시작하려 합니다. 
3장에 이어서 LED 드라이버를 완성하는 것을 목표로 합니다. 

# 단순하게 시작해서 솔루션 키워가기

이전장의 단순무식한 구현은 점진적으로 더 견고해질 것입니다. 

다음으로 추가할 테스트는 두개의 LED를 켜는 것입니다. 

이전에 설명한바와 같이 테스트 먼저 추가해보겟습니다. 

```c
void TurnOnMultipleLeds (void ** state) {
  LedDriver_TurnOn(9);
  LedDriver_TurnOn(8);
  assert_int_equal(0x180, virtualLeds);
}
```

테스트는 쉬워야 합니다. 
위와 같이 테스트를 작성하였습니다. 
예상하는 결과는 8번 LED 와 9번 LED 가 켜져야 됩니다. 
이때 `virtualLeds` 의 값은 `00000001 10000000` 입니다. 
즉 0x0180 입니다. 

이제 테스트를 실행해보겠습니다. 

```
[==========] Running 4 test(s).
CMocka setup
[ RUN      ] LedsOffAfterCreate
[       OK ] LedsOffAfterCreate
[ RUN      ] TurnOnLedOne
[       OK ] TurnOnLedOne
[ RUN      ] TurnOnLedOff
[       OK ] TurnOnLedOff
[ RUN      ] TurnOnMultipleLeds
[  ERROR   ] --- 0x180 != 0x1
[   LINE   ] --- led_driver_test.c:45: error: Failure!
[  FAILED  ] TurnOnMultipleLeds
CMocka teardown
[==========] 4 test(s) run.
[  PASSED  ] 3 test(s).
[  FAILED  ] 1 test(s), listed below:
[  FAILED  ] TurnOnMultipleLeds

 1 FAILED TEST(S)
```

테스트는 예상대로 실패합니다. 
`LedDriver_TurnOn` 함수는 현재 1번 LED를 켜는 기능만 하기 때문입니다. 
이제 `LedDriver_TurnOn` 함수를 수정해보겠습니다. 

```c
void LedDriver_TurnOn(int ledNumber){
  *ledsAddress |= (1 << ledNumber);
}
```

위 코드는 `ledNumber` 의 위치의 비트를 or 연산을 이용해 1로 변경시킵니다. 
이제 다시 테스트를 실행해보겠습니다. 

```
[==========] Running 4 test(s).
CMocka setup
[ RUN      ] LedsOffAfterCreate
[       OK ] LedsOffAfterCreate
[ RUN      ] TurnOnLedOne
[  ERROR   ] --- 0x1 != 0x2
[   LINE   ] --- led_driver_test.c:33: error: Failure!
[  FAILED  ] TurnOnLedOne
[ RUN      ] TurnOnLedOff
[       OK ] TurnOnLedOff
[ RUN      ] TurnOnMultipleLeds
[  ERROR   ] --- 0x180 != 0x300
[   LINE   ] --- led_driver_test.c:45: error: Failure!
[  FAILED  ] TurnOnMultipleLeds
CMocka teardown
[==========] 4 test(s) run.
[  PASSED  ] 2 test(s).
[  FAILED  ] 2 test(s), listed below:
[  FAILED  ] TurnOnLedOne
[  FAILED  ] TurnOnMultipleLeds

 2 FAILED TEST(S)
```

예상과는 다르게 테스트가 두개나 실패합니다.
`TurnOnLedOne` 테스트와 `TurnOnMultipleLeds` 테스트에서 실패하였습니다. 
코드는 단지 한줄밖에 수정하지 않았습니다. 
하지만 원치 않은 곳에서 에러가 발생합니다. 
분명 작은 실수지만 이러한 사이드이펙트는 생각보다 자주 발생합니다. 
TDD는 이처럼 문제가 아직 작은 상태일 때 발견할 수 있다는 점입니다. 

이제 문제를 해결해보겠습니다. 

먼저 `TurnOnLedOne` 의 예상결과는 0x0001이지만 결과는 0x0002 가 되었습니다. 
비트가 하나 더 이동했다는 것을 알 수 있습니다. 

`LedDriver_TurnOn` 함수를 다시 수정하겠습니다. 

```c
void LedDriver_TurnOn(int ledNumber){
  *ledsAddress |= 1 << (ledNumber - 1);
}
```

```
[==========] Running 4 test(s).
CMocka setup
[ RUN      ] LedsOffAfterCreate
[       OK ] LedsOffAfterCreate
[ RUN      ] TurnOnLedOne
[       OK ] TurnOnLedOne
[ RUN      ] TurnOnLedOff
[       OK ] TurnOnLedOff
[ RUN      ] TurnOnMultipleLeds
[       OK ] TurnOnMultipleLeds
CMocka teardown
[==========] 4 test(s) run.
[  PASSED  ] 4 test(s).
```

테스트가 모두 통과하였습니다. 

테스트가 모두 통과한 이 시점에서 코드를 개선할 부분이 있는지 확인합니다. 
보기 좋지 않은 코드가 있다면 바로 리팩터링을 수행해줍니다. 

아래와 같이 비트 처리 부분을 도움 함수로 리팩터링해줍니다. 

```c
static uint16_t convertLedNumberToBit(int ledNumber) {
  return 1 << (ledNumber - 1);
}

void LedDriver_TurnOn(int ledNumber){
  *ledsAddress |= convertLedNumberToBit(ledNumber);
}
```
이 작업은 의도를 드러낼 수 있는 이름으로 포장하여 가독성을 올렸습니다. 
전역 네임스페이스에 추가되는 것을 피해기 위해 static 키워드를 사용했습니다. 
`convertLedNumberToBit` 함수를 인라인이나 메크로로 처리하여 성능을 올릴 수 있지만, 컴파일러의 최적화라면 불필요한 작업입니다. 

## 꾸준한 진행 

하나하나의 테스트가 구현을 점차 완성시킵니다. 

다양한 테스트를 추가하여 프로그램의 안정성을 올릴 수 있습니다. 
다른 테스트 하나를 더 추가해보겠습니다 .

```
void TurnOffAnyLed (void ** state) {
  LedDriver_TurnOn(9);
  LedDriver_TurnOn(8);
  LedDriver_TurnOff(8);
  assert_int_equal(0x100, virtualLeds);
}
```

이제 테스트를 돌려봅니다. 

```
[==========] Running 5 test(s).
CMocka setup
[ RUN      ] LedsOffAfterCreate
[       OK ] LedsOffAfterCreate
[ RUN      ] TurnOnLedOne
[       OK ] TurnOnLedOne
[ RUN      ] TurnOnLedOff
[       OK ] TurnOnLedOff
[ RUN      ] TurnOnMultipleLeds
[       OK ] TurnOnMultipleLeds
[ RUN      ] TurnOffAnyLed
[  ERROR   ] --- 0x100 != 0
[   LINE   ] --- led_driver_test.c:52: error: Failure!
[  FAILED  ] TurnOffAnyLed
CMocka teardown
[==========] 5 test(s) run.
[  PASSED  ] 4 test(s).
[  FAILED  ] 1 test(s), listed below:
[  FAILED  ] TurnOffAnyLed

 1 FAILED TEST(S)
```

예상했던대로 테스트는 실패합니다. 
앞에서 작성한 `TurnOnLedOff` 함수는 모든 LED를 끄기 때문입니다. 
`TurnOnLedOff`를 비트 마스크 처리하는 코드로 작성 후 테스트 하려면 다른 LED를 미리 ON 상태로 만들어줘야 합니다. 

이를 위해 모두켜는 함수를 만들려 합니다. 
먼저 테스트를 추가하겠습니다. 

```c
void AllOn (void ** state) {
  LedDriver_TurnAllOn();
  assert_int_equal(0xffff, virtualLeds);
}
```

```c
enum {ALL_LEDS_ON = ~0, ALL_LEDS_OFF = ~ALL_LEDS_ON};

void LedDriver_TurnAllOn(void) {
  *ledsAddress = ALL_LEDS_ON;
}
```

위와 같이 모든 LED를 켤 수 있는 기능이 추가되었습니다. 
이제 특정 LED를 끄는 테스트를 수정합니다. 

```c
void TurnOffAnyLed (void ** state) {
  LedDriver_TurnAllOn();
  LedDriver_TurnOff(8);
  assert_int_equal(0xff7f, virtualLeds);
}
```

```c
void LedDriver_TurnOff(int ledNumber) {
  *ledsAddress &= ~(convertLedNumberToBit(ledNumber));
}
```

이제 다시 테스트를 수행하면 통과하는 것을 확인할 수 있습니다. 
```
[==========] Running 6 test(s).
CMocka setup
[ RUN      ] LedsOffAfterCreate
[       OK ] LedsOffAfterCreate
[ RUN      ] TurnOnLedOne
[       OK ] TurnOnLedOne
[ RUN      ] TurnOnLedOff
[       OK ] TurnOnLedOff
[ RUN      ] TurnOnMultipleLeds
[       OK ] TurnOnMultipleLeds
[ RUN      ] TurnOffAnyLed
[       OK ] TurnOffAnyLed
[ RUN      ] AllOn
[       OK ] AllOn
CMocka teardown
[==========] 6 test(s) run.
[  PASSED  ] 6 test(s).
```
