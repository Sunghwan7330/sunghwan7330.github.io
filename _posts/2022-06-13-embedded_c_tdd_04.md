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

다음으로 소프트웨어에서 LED의 ON/OFF 상태를 읽을 수 있는지에 대한 테스트를 추가하려 합니다. 
하드웨어에서 값을 읽지 않았다는 것을 확인하기 위해 아래와 같은 테스트를 추가합니다. 
새로 추가하는 테스트에서 virtualLeds를 0xffff로 먼저 설정하면 드라이버가 LED의 현재 상태를 하드웨어로 부터 읽어오지 않는다는 것을 확인할 수 있습니다. 
```c
void LedMemoryIsNotReadable (void ** state) {
  virtualLeds = 0xffff;
  LedDriver_TurnOn(8);
  assert_int_equal(0x80, virtualLeds);
}
```

```
[==========] Running 7 test(s).
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
[ RUN      ] LedMemoryIsNotReadable
[  ERROR   ] --- 0x80 != 0xffff
[   LINE   ] --- led_driver_test.c:62: error: Failure!
[  FAILED  ] LedMemoryIsNotReadable
CMocka teardown
[==========] 7 test(s) run.
[  PASSED  ] 6 test(s).
[  FAILED  ] 1 test(s), listed below:
[  FAILED  ] LedMemoryIsNotReadable

 1 FAILED TEST(S)
```

테스트가 실패하는것을 보았을 때 LED의 현재 상태를 하드웨어로 부터 읽어오지 않는다는 것을 확인할 수 있습니다. 

테스트를 통과시키기 위해, LED의 상태 를 ledsImage라는 파일 범위 비공개 변수에 기록하도록 합니다. 
LedDriver_Create()에서 이 변수를 초기화합니다. 

```c
uint16_t * ledsAddress;
static uint16_t ledsImage;

void LedDriver_Create(uint16_t * address) {
  ledsAddress = address;
  ledsImage = ALL_LEDS_OFF;
  *ledsAddress = ledsImage;
}
```

LedDriver_TurnOn(), LedDriver_TurnOff(), LedDriver_TurnAllOn() 함수에서 현 재 LED 상태를 알기 위해 ledsImage 변수를 이용합니다.

```c
void LedDriver_TurnOn(int ledNumber){
  ledsImage |= convertLedNumberToBit(ledNumber);
  *ledsAddress = ledsImage;
}

void LedDriver_TurnOff(int ledNumber) {
  ledsImage &= ~(convertLedNumberToBit(ledNumber));
  *ledsAddress = ledsImage;
}

void LedDriver_TurnAllOn(void) {
  ledsImage = ALL_LEDS_ON;
  *ledsAddress = ledsImage;
}
```

이제 모든 테스트가 통과하는 것을 확인하였습니다. 

```
[==========] Running 7 test(s).
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
[ RUN      ] LedMemoryIsNotReadable
[       OK ] LedMemoryIsNotReadable
[==========] 7 test(s) run.
[  PASSED  ] 7 test(s).
```

이후 코드 여러 곳에 중복된 `*ledsAddress = ledsImage;` 를 추출하여 도움 함수로 만들 고 향후 코드를 읽는 사람들이 코드를 쉽게 이해하도록 변경해줍니다. 

```c
static void updateHardware(void) {
  *ledsAddress = ledsImage;
}

void LedDriver_TurnAllOn(void) {
  ledsImage = ALL_LEDS_ON;
  updateHardware();
}
```

## 경계 조건 테스트 

다음 테스트는 LED 번호의 정상값 범위를 상한값과 하한값으로 확인합니다. 
이 테스트는 상세 요구사항 역할을 합니다. 

```c
void UpperAndLowerBounds (void** state) {
  LedDriver_TurnOn(1);
  LedDriver_TurnOn(16);
  assert_int_equal(0x80, virtualLeds);
}
```

위의 테스트는 LED의 유효 범위 내의 값을 제어하기 때문에 테스트 수행시 통과하게 됩니다. 

```
[==========] Running 8 test(s).
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
[ RUN      ] LedMemoryIsNotReadable
[       OK ] LedMemoryIsNotReadable
[ RUN      ] UpperAndLowerBounds
[       OK ] UpperAndLowerBounds
[==========] 8 test(s) run.
[  PASSED  ] 8 test(s).
```

LED 범위가 정상 범위를 벗어나면 어떤 동작을 해야될까요?

1. 드라이버가 인접한 메모리 값을 쓴다?
2. 잘못된 값이므로 무시한다?

2번이 더 좋은 방법으로 판단됩니다. 

우선은 경계를 벗어나는 값이 입력되었을 때 어떤 결과가 나오는지 확인해봅니다. 

```c
void OutOfBoundsChangesNothing (void** state) {
  LedDriver_TurnOn(-1);
  LedDriver_TurnOn(0);
  LedDriver_TurnOn(1);
  LedDriver_TurnOn(2);
  LedDriver_TurnOn(17);
  LedDriver_TurnOn(33);
  LedDriver_TurnOn(3141);
  assert_int_equal(0, virtualLeds);
}
```

위의 테스트를 추가하고 테스트를 수행해보겠습니다. 

```
[==========] Running 9 test(s).
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
[ RUN      ] LedMemoryIsNotReadable
[       OK ] LedMemoryIsNotReadable
[ RUN      ] UpperAndLowerBounds
[       OK ] UpperAndLowerBounds
[ RUN      ] OutOfBoundsChangesNothing
[  ERROR   ] --- 0 != 0x3
[   LINE   ] --- led_driver_test.c:76: error: Failure!
[  FAILED  ] OutOfBoundsChangesNothing
[==========] 9 test(s) run.
[  PASSED  ] 8 test(s).
[  FAILED  ] 1 test(s), listed below:
[  FAILED  ] OutOfBoundsChangesNothing

 1 FAILED TEST(S)
```

유효범위 외의 값은 무시된 듯 해보입니다. 
(서적에서는 시프트 연산이 범위를 벗어나는 경우 로테이트 되었음)

하지만 경계조건을 처리하지 않은 코드를 테스트하다보면 테스트가 실행되다가 크래시가 발생하는 경우가 있습니다. 
스택상의 배열의 경계를 벗어나 스택이 깨질 수도 있습니다. 
우리가 작성한 테스트에서는 이러한 손상이 발생하지는 않았지만, 경계처리는 꼭 해주어야 합니다. 

아래와 같이 LedDriver_TurnOn()와 LedDriver_TurnOff()에 보호절을 추가합니다. 

```c
void LedDriver_TurnOn(int ledNumber){
  if (ledNumber <= 0 || ledNumber > 16)
    return;

  ledsImage |= convertLedNumberToBit(ledNumber);
  updateHardware();
}

void LedDriver_TurnOff(int ledNumber) {
  if (ledNumber <= 0 || ledNumber > 16)
    return;

  ledsImage &= ~(convertLedNumberToBit(ledNumber));
  updateHardware();
}
```

이후 테스트의 결과값을 수정하여 테스트가 통과하도록 해줍니다. 

```
[==========] Running 9 test(s).
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
[ RUN      ] LedMemoryIsNotReadable
[       OK ] LedMemoryIsNotReadable
[ RUN      ] UpperAndLowerBounds
[       OK ] UpperAndLowerBounds
[ RUN      ] OutOfBoundsChangesNothing
[       OK ] OutOfBoundsChangesNothing
[==========] 9 test(s) run.
[  PASSED  ] 9 test(s).
```

이 수정에서 실수한 부분이 있습니다. 
LedDriver_TurnOff 에 대한 테스트를 작성하지 않고 수정을 먼저 진행했습니다. 

물론 테스트된 코드를 복사하였으므로 안전하다고 생각할 수 있지만, 이는 조심해야 합니다. 

이제 빠진 LedDriver_TurnOff 에 대해서 경계를 벗어날때에 대한 테스트를 추가해줍니다. 

```c
void OutOfBoundsTurnOffDoesNoHarm (void** state) {
  LedDriver_TurnOff(-1);
  LedDriver_TurnOff(0);
  LedDriver_TurnOff(17);
  LedDriver_TurnOff(3141);
  assert_int_equal(0xffff, virtualLeds);
}
```

이제 테스트를 실행해보겠습니다. 

```
[==========] Running 10 test(s).
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
[ RUN      ] LedMemoryIsNotReadable
[       OK ] LedMemoryIsNotReadable
[ RUN      ] UpperAndLowerBounds
[       OK ] UpperAndLowerBounds
[ RUN      ] OutOfBoundsChangesNothing
[       OK ] OutOfBoundsChangesNothing
[ RUN      ] OutOfBoundsTurnOffDoesNoHarm
[       OK ] OutOfBoundsTurnOffDoesNoHarm
[==========] 10 test(s) run.
[  PASSED  ] 10 test(s).
```

예상과 다르게 테스트가 성공했습니다. 
테스트에 성공한 이유는 시작시 모든 LED가 켜져있기 때문입니다. 
따라서 테스트 시작시에 모든 LED를 켜고 시작해야 합니다. 

아래와 같이 테스트를 수정하고 다시 진행해보겠습니다. 

```c
void OutOfBoundsTurnOffDoesNoHarm (void** state) {
  LedDriver_TurnAllOn();
  LedDriver_TurnOff(-1);
  LedDriver_TurnOff(0);
  LedDriver_TurnOff(17);
  LedDriver_TurnOff(3141);
  assert_int_equal(0xffff, virtualLeds);
}
```

```
[==========] Running 10 test(s).
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
[ RUN      ] LedMemoryIsNotReadable
[       OK ] LedMemoryIsNotReadable
[ RUN      ] UpperAndLowerBounds
[       OK ] UpperAndLowerBounds
[ RUN      ] OutOfBoundsChangesNothing
[       OK ] OutOfBoundsChangesNothing
[ RUN      ] OutOfBoundsTurnOffDoesNoHarm
[       OK ] OutOfBoundsTurnOffDoesNoHarm
[==========] 10 test(s) run.
[  PASSED  ] 10 test(s).
```

테스트가 통과하는 것을 확인하였습니다. 

이제 드라이버의 오류에 대해서 알람을 주는 기능을 추가해야 합니다. 

`RuntimeError.h` 헤더 파일에 아래의 코드를 추가합니다. 

```c
void RuntimeError(const char * message, int parameter, const char * file, int line);

#define RUNTIME_ERROR(description, parameter)\
 RuntimeError(description, parameter, __FILE__, __LINE__)
```

RuntimeError는 이벤트 로그에 오류 메시지를 추가합니다. 
테스트 중 에는 RuntimeError()를 스텁으로 만들어서 마지막으로 발생한 오류를 저장했다가 확인할 수 있게 합니다. 
`RuntimeErrorStub.h` 생성 후 아래와 같이 작성해줍니다. 

```c
void RuntimeErrorStub_Reset(void); 
const char * RuntimeErrorStub_GetLastError(void); 
int RuntimeErrorStub_GetLastParameter(void);
void RuntimeError(const char * m, int p, const char * f, int l);
```

스텁의 구현은 아래와 같습니다. 

```
#include "RuntimeErrorStub.h"

static const char * message = "No Error";
static int parameter = -1;
static const char * file = 0;
static int line = -1;

void RuntimeErrorStub_Reset(void) {
  message = "No Error";
  parameter = -1;
}

const char * RuntimeErrorStub_GetLastError(void) {
  return message;
}

void RuntimeError(const char * m, int p, const char * f, int l) {
  message = m;
  parameter = p;
  file = f;
  line = l;
}
int RuntimeErrorStub_GetLastParameter(void) {
  return parameter;
}
```

RuntimeError()의 스텁 버전은 오류 정보를 저장하기만 합니다. 

테스트 중에는 RuntimeError()의 스텁 버전이 링크됩니다. 
이로써 테스트 케이스가 경계를 벗어나는 경우에 RuntimeError()가 호출되는지 여부를 확인할 수 있습니다. 

아래의 테스트를 추가해줍니다. 

```c
void OutOfBoundsProducesRuntimeError (void** state) {
  LedDriver_TurnOn(-1);
  assert_string_equal("LED Driver: out-of-bounds LED", RuntimeErrorStub_GetLastError());
  assert_int_equal(-1, RuntimeErrorStub_GetLastParameter());
}
```

이제 테스트를 수행해줍니다. 

```
[==========] Running 11 test(s).
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
[ RUN      ] LedMemoryIsNotReadable
[       OK ] LedMemoryIsNotReadable
[ RUN      ] UpperAndLowerBounds
[       OK ] UpperAndLowerBounds
[ RUN      ] OutOfBoundsChangesNothing
[       OK ] OutOfBoundsChangesNothing
[ RUN      ] OutOfBoundsTurnOffDoesNoHarm
[       OK ] OutOfBoundsTurnOffDoesNoHarm
[ RUN      ] OutOfBoundsProducesRuntimeError
[  ERROR   ] --- "LED Driver: out-of-bounds LED" != "No Error"
[   LINE   ] --- led_driver_test.c:92: error: Failure!
[  FAILED  ] OutOfBoundsProducesRuntimeError
[==========] 11 test(s) run.
[  PASSED  ] 10 test(s).
[  FAILED  ] 1 test(s), listed below:
[  FAILED  ] OutOfBoundsProducesRuntimeError

 1 FAILED TEST(S)
```

이제 RUNTIME_ERROR( ) 호출을 추가해 테스트가 통과하도록 해줍니다. 

```c
void LedDriver_TurnOn(int ledNumber){
  if (ledNumber <= 0 || ledNumber > 16){
	RUNTIME_ERROR("LED Driver: out-of-bounds LED", -1);
    return;
  }

  ledsImage |= convertLedNumberToBit(ledNumber);
  updateHardware();
}
```

테스트를 실행해줍니다. 

```
[==========] Running 11 test(s).
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
[ RUN      ] LedMemoryIsNotReadable
[       OK ] LedMemoryIsNotReadable
[ RUN      ] UpperAndLowerBounds
[       OK ] UpperAndLowerBounds
[ RUN      ] OutOfBoundsChangesNothing
[       OK ] OutOfBoundsChangesNothing
[ RUN      ] OutOfBoundsTurnOffDoesNoHarm
[       OK ] OutOfBoundsTurnOffDoesNoHarm
[ RUN      ] OutOfBoundsProducesRuntimeError
[       OK ] OutOfBoundsProducesRuntimeError
[==========] 11 test(s) run.
[  PASSED  ] 11 test(s).
```

의도대로 통과하는 것을 확인하였습니다.

# 코드를 깔끔하게 유지하기 - 자주 리팩터링 하기

예제코드를 작성하면서 일부 작은 문제들을 제거하기 위해 리팩터링을 했습니다. 
리팩터링을 할 것이 보이게 된다면 바로 진행하여 더 큰 문제가 자라날 기회를 없애는 것이 좋습니다. 
단, 리팩터링은 모든 테스트가 통화하는 경우에만 진행하여야 합니다. 

지금은 중복 코드를 추출해서 도움함수로 만들고, 매직 넘버 대신 상수 정의를 도입해서 두가지의 냄새를 제거해보려 합니다. 

## 잘라내기 대신 복사하기

새로운 함수를 추출할 때, 중복코드 잘라내기(cut) 대신 복사하기(copy)를 사용해야 합니다. 
새로 만들 함수에 뼈대만 추가하고 복사한 코드를 함수에 넣습니다. 
새 함수에 인자나 반환값이 필요하면 이를 추가한 후 컴파일을 합니다. 

테스트가 통과하면 중복 코드가 사용되고 있는 다른곳들도 새로 만든 도움함수로 치환합니다. 

새 도움함수가 적용되고 테스트가 모두 통과한 다음 매직 넘버를 상수로 치환합니다. 

```c
enum {FIRST_LED = 1, LAST_LED = 16};
static BOOL IsLedOutOfBounds(int ledNumber) {
  return (ledNumber < FIRST_LED) || (ledNumber > LAST_LED);
}
```

`IsLedOutOfBounds` 함수는 외부에서 호출할 필요가 없으므로 헤더에 추가하지 않고 static으로 선언합니다. 
리팩터링된 `LedDriver_TurnOn` 과 `LedDriver_TurnOff`는 아래와 같다.

```c
void LedDriver_TurnOn(int ledNumber){
  if (IsLedOutOfBounds(ledNumber)) {
    RUNTIME_ERROR("LED Driver: out-of-bounds LED", -1);
    return;
  }

  ledsImage |= convertLedNumberToBit(ledNumber);
  updateHardware();
}

void LedDriver_TurnOff(int ledNumber) {
  if (IsLedOutOfBounds(ledNumber)) {
    RUNTIME_ERROR("LED Driver: out-of-bounds LED", -1);
    return;
  }

  ledsImage &= ~(convertLedNumberToBit(ledNumber));
  updateHardware();
}
```

비트 조작 코드는 아래와 같이 함수를 추출해주겠습니다. 

```c
static void setLedImageBit(int ledNumber) {
  ledsImage |= convertLedNumberToBit(ledNumber);
}

static void clearLedImageBit(int ledNumber) {
  ledsImage &= ~convertLedNumberToBit(ledNumber);
}

void LedDriver_TurnOn(int ledNumber){
  if (IsLedOutOfBounds(ledNumber)) {
    RUNTIME_ERROR("LED Driver: out-of-bounds LED", -1);
    return;
  }

  setLedImageBit(ledNumber);
  updateHardware();
}

void LedDriver_TurnOff(int ledNumber) {
  if (IsLedOutOfBounds(ledNumber)) {
    RUNTIME_ERROR("LED Driver: out-of-bounds LED", -1);
    return;
  }

  clearLedImageBit(ledNumber);
  updateHardware();
}
```

## 한번에 하나씩 문제 해결하기 

작은 단계를 밟아가는 것은 여러분이 한 번에 하나씩 문제를 해결하는 데 집중하 도록 도와줍니다. 
 사람은 한 번에 하나의 문제만 해결할 때 일을 더 잘 할 수 있습니다. 

리팩터링 결과로 이전에 동작하던 테스트가 실패하면 디버깅을 하지 말라 합니다. 
되돌리기(undo) 한 다음 여러분이 작업한 내용을 꼼꼼하게 살펴 보기를 바랍니다. 
문제가 정말 명확하다면 바로 고쳐보는 것도 좋지만, 다시 녹색 상태로 돌아가려면 되돌리기를 얼마나 해야 하는지 의식하고 있어야 합니다. 
만약 한두 군데 고쳐 봐서 테스트가 통과하지 않는다면 스스로의 구멍을 파고 있는 상황이 됩니다.
 파는 것을 멈추고 되돌려서 다시 생각하는 것이 좋습니다. 

# 완료될 때 까지 반복하기 

드라이버의 핵심 기능은 갖춰졌습니다. 
이제 뼈대에 살을 붙일때 까지 테스트와 제품 코드를 계속 추가할 수 있습니다. 

이제 LED 상태를 가져오는 기능을 추가하겠습니다. 
먼저 테스트부터 추가해줍니다. 

```c
void IsOn(void** state) {
  assert_int_equal(0, (LedDriver_IsOn(11)));
  LedDriver_TurnOn(11);
  assert_int_equal(1, (LedDriver_IsOn(11)));
}
```

컴파일 실패하는 것을 확인하고 `IsOn` 함수를 헤더에 추가해줍니다. 
컴파일 성공하는것을 확인해고 이제 함수를 구현합니다. 

```c
bool LedDriver_IsOn(int ledNumber) {
  return false;
}
```

이 처럼 하드코딩된 코드를 추가하면 테스트에 실패하는 것을 확인할 수 있습니다. 

이제 테스트가 통과하도록 코드를 수정해줍니다. 

```c
bool LedDriver_IsOn(int ledNumber) {
  return ledsImage & (convertLedNumberToBit(ledNumber));
}
```

```
[==========] Running 12 test(s).
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
[ RUN      ] LedMemoryIsNotReadable
[       OK ] LedMemoryIsNotReadable
[ RUN      ] UpperAndLowerBounds
[       OK ] UpperAndLowerBounds
[ RUN      ] OutOfBoundsChangesNothing
[       OK ] OutOfBoundsChangesNothing
[ RUN      ] OutOfBoundsTurnOffDoesNoHarm
[       OK ] OutOfBoundsTurnOffDoesNoHarm
[ RUN      ] OutOfBoundsProducesRuntimeError
[       OK ] OutOfBoundsProducesRuntimeError
[ RUN      ] IsOn
[       OK ] IsOn
[==========] 12 test(s) run.
[  PASSED  ] 12 test(s).
```
