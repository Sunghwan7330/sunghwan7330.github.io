---
title: "[임베디드 C를 윈한 TDD] 3장 모듈 시작하기"
excerpt: "해당 내용은 '임베디드 C를 위한 TDD' 책의 3장 내용을 정리하였습니다. "

categories:
  - 'embedded_c_tdd'
tags:
  - 임베디드
  - TDD

toc: true
toc_sticky: true

date: 2022-05-22
last_modified_at: 2022-05-22
---

# 개요 

본 장에서는 첫 예제인 LED 드라이버를 제작하면서 TDD를 시작하려 합니다. 

# LED 드라이버가 하는 일 

LED 드라이버의 요구사항은 아래와 같습니다. 

1. LED 드라이버는 2가지 상태를 가지는 16개의 LED를 제어한다. 
2. 드라이버는 다른 LED에 영향을 주지 않고 각 LED를 On/Off 시킬 수 있다. 
3. 드라이버는 한 번의 인터페이스 함수 호출로 모든 LED를 On/Off 시킬 수 있다. 
4. 드라이버 사용자는 임의의 LED 상태를 조회할 수 있다. 
5. 전원이 공급되면 하드웨어는 기본적으로 LED를 On 상태로 만든다. 소프트웨 어에서 Off 시켜야만 한다. 
6. LED는 단일 16비트 워드(메모리 주소는 나중에 정해짐)로 메모리에 매핑된다.
7. 비트가 ‘1’이 되면 해당 LED가 켜지고 ‘0’이 되면 해당 LED가 꺼진다. 
8. 최하위 비트가 1번 LED에, 최상위 비트가 16번 LED에 해당한다.

1~4 까지는 LED 드라이버가 하는 일에 관한 것입니다. 
5~8 까지는 드라이버와 하드웨어가 어떻게 상호작용하는지를 설명합니닫. 

설계를 시작하기 전 어떠한 테스트가 필요할지를 미리 생각합니다. 

* 드라이버가 초기화된 후에 모든 LED는 off 상태다.
* LED 하나를 켤 수 있다.
* LED 하나를 끌 수 있다.
* 여러 개의 LED를 켜거나 끌 수 있다.
* LED 모두 켜기
* LED 모두 끄기
* LED 상태 얻어오기
* 경계 값 확인
* 유효 범위를 벗어난 값 확인

# 첫 테스트 작성 

참고하는 서적에서는 unity 라는 테스트 하니스를 사용하지만, 저는 cmocka를 이용하여 진행하겠습니다. 

```c
#include <stdarg.h>
#include <stddef.h>
#include <setjmp.h>
#include <cmocka.h>

#include <stdint.h>

#include "led_driver.h"

/* These functions will be used to initialize
   and clean resources up after each test run */
int setup (void ** state) {
  print_message("CMocka setup\n");
  return 0;
}

int teardown (void ** state) {
  print_message("CMocka teardown\n");
  return 0;
}

void LedsOffAfterCreate(void ** state) {
  uint16_t virtualLeds = 0xffff;
  LedDriver_Create(&virtualLeds);
  assert_int_equal(0, virtualLeds);
}


int main (void) {
    const struct CMUnitTest tests [] =
    {
        cmocka_unit_test_setup_teardown(LedsOffAfterCreate, setup, teardown),
    };

    /* If setup and teardown functions are not
       needed, then NULL may be passed instead */

    int count_fail_tests =
        cmocka_run_group_tests (tests, setup, teardown);

    return count_fail_tests;
}

```

먼저 setup, teardown 함수를 정의합니다. 
setup는 테스트 시작 전 호출되는 함수이며, teardown 은 테스트 종료 후 호출되는 함수입니다. 
tests 에서는 필요한 테스트 목록을 작성하기 위한 배열입니다. 

가장 먼저 LedsOffAfterCreate 라는 함수를 테스트하려 합니다. 
LedsOffAfterCreate 함수는 led가 초기화되서 전부 꺼지는지를 테스트하는 함수입니다. 
함수에서는 가상의 LED 값을 설정하고 LedDriver_Create에 해당 주소를 넣었을 때 가상의 LED 가 모두 꺼지는지를 확인합니다. 
이때 가상 LED의 값은 0이여야 합니다. 
`assert_int_equal(0, virtualLeds);` 는 virtualLeds의 값이 0이여야 테스트가 통과함을 의미합니다. 


이후 실제 LED 드라이버를 구현하는 코드를 작성해줍니다. 

```c
#include "led_driver.h"

void LedDriver_Create(uint16_t * address) {
}
void LedDriver_Destroy(void) {
}

```

위와 같이 작성 후 컴파일하여 테스트를 실행해보겠습니다. 

```
[==========] Running 1 test(s).
CMocka setup
[ RUN      ] LedsOffAfterCreate
[  ERROR   ] --- 0 != 0xffff
[   LINE   ] --- led_driver_test.c:25: error: Failure!
[  FAILED  ] LedsOffAfterCreate
CMocka teardown
[==========] 1 test(s) run.
[  PASSED  ] 0 test(s).
[  FAILED  ] 1 test(s), listed below:
[  FAILED  ] LedsOffAfterCreate

 1 FAILED TEST(S)

```

예상대로 테스트에 실패합니다. 
이제 테스트가 실패하지 않도록 `LedDriver_Create` 함수를 작성해줍니다. 

```c
void LedDriver_Create(uint16_t * address) {
  *address = 0;
}
```

다시 컴파일한 뒤 테스트를 실행해보겠습니다. 

```
[==========] Running 1 test(s).
CMocka setup
[ RUN      ] LedsOffAfterCreate
[       OK ] LedsOffAfterCreate
CMocka teardown
[==========] 1 test(s) run.
[  PASSED  ] 1 test(s).
```

테스트가 통과하였습니다. 
`LedDriver_Create` 가 의도대로 동작하고 있음을 의미합니다. 

# 의존성 주입 

`LedDriver_Create` 함수를 호출할 때 `virtualLeds` 를 전달하는 것은 ‘의존성 주입’ 을 사용한 것입니다. 
컴파일 시 LED 주소를 고정하여 의존관계를 맺게 하는 대신 주소를 전달합니다.
이렇게 하면 타깃 시스템의 초기화 함수만 물리적인 LED 주소에 대해 컴파일 시점의 의존성을 갖게 됩니다. 

의존성 주입 사용에 따른 부가적인 이익은 LED Driver의 재사용성이 높아진다는 점입니다. 
드라이버를 라이브러리에 두고 LED 주소를 가지는 시스템에서 사용할 수 있습니다. 
이는 TDD가 자연스럽고 유연하게 설계를 가져온다는 것을 보여줍니다. 

# 테스트에 앞서 코드를 작성하지 말것 

아래는 밥 마틴의 TDD의 3원칙입니다. 

* 실패하는 단위 테스트를 통과시키기 위한 경우에만 제품 코드를 작성하라.
* 실패하는 단 하나의 단위 테스트만 작성하라. 빌드 실패도 실패다. 
* 실패하는 단 하나의 단위 테스트를 통과시킬 만큼만 제품 코드를 작성하라.
* http://ArticleS.UncleBob.TheThreeRulesOfTdd (butunclebob.com) 참고 

테스트 주도 개발은 테스트를 성공하기 위해 개발을 하는 방법론입니다. 
테스트를 먼저 작성합니다. 
이후 이 실패한 테스트를 성공하기 위한 코드를 작성하는 것입니다. 

# 먼저 인터페이스를 테스트 주도로 개발하기 

잘 설계된 모듈은 훌륭한 인터페이스가 필수적입니다. 
처음 몇 개의 테스트는 인터페이스 설계를 이끌어냅니다. 
인터페이스에 집중한다는 것은 개발하는 코드를 바깥에서 안으로 만들어간다는 것을 의미합니다. 
이는 사용자 관점에서 더 편리한 인터페이스를 얻을 수 있습니다. 

인터페이스를 구현할 때는 우선 하드코딩된 값을 반환하는 것부터 시작합니다. 
여기서 핵심은 테스트가 아니라 인터페이스 설계를 유도하고 간단한 경계값 테스트를 얻는 것입니다. 

드라이버의 주요 목적은 LED 를 On / Off 하는 것입니다. 
LED 마다 01부터 16까지 숫자가 붙어있습니다. 
만약 1번 LED를 켜려면 드라이버가 0x0001을 LED의 메모리 매핑 주소에 쓰면 됩니다. 

이제 LED를 켜는 테스트 코드를 작성하겠습니다. 

```c
void TurnOnLedOne(void ** state) {
  uint16_t virtualLeds = 0xffff;
  LedDriver_Create(&virtualLeds);
  LedDriver_TurnOn(1);
  assert_int_equal(1, virtualLeds);
}
```

이와 같이 테스트를 작성하고 컴파일을 하면 컴파일 오류가 발생합니다. 
LED 드라이버의 헤더에 함수 프로토타입을 작성하고 c파일에 뼈대만 입력해줍니다. 

```c
void LedDriver_TurnOn(int ledNumber) {
}
```

이제 컴파일에 성공하게 될것입니다. 
컴파일 후 테스트를 수행하면 결과가 실패하는 것을 알 수 있습니다. 

```
[==========] Running 2 test(s).
CMocka setup
[ RUN      ] LedsOffAfterCreate
[       OK ] LedsOffAfterCreate
[ RUN      ] TurnOnLedOne
[  ERROR   ] --- 0x1 != 0
[   LINE   ] --- led_driver_test.c:32: error: Failure!
[  FAILED  ] TurnOnLedOne
CMocka teardown
[==========] 2 test(s) run.
[  PASSED  ] 1 test(s).
[  FAILED  ] 1 test(s), listed below:
[  FAILED  ] TurnOnLedOne

 1 FAILED TEST(S)
```

이 함수를 통과하기 위해서는 `LedDriver_TurnOn` 함수에서 LED 메모리의 주소를 1로 변경해야 합니다. 
그러기 위해서는 먼저 `LedDriver_Create` 함수에서 입력되는 LED 메모리 주소의 값을 보관해야합니다. 

```c
uint16_t * ledsAddress;
void LedDriver_Create(uint16_t * address) {
  ledsAddress = address
  *address = 0;
}
```
이와 같이 전역변수로 LED 메모리 주소를 가지고 있으면 `LedDriver_TurnOn` 함수에서 
LED 메모리주소의 값을 변경할 수 있습니다. 
위의 테스트에서 가장 간단하게 해결할 수 있는 방법은 `LedDriver_TurnOn` 함수에서 LED 메모리의 값을 1로 변경하는 것입니다. 

```c
void LedDriver_TurnOn(int ledNumber){
  *ledsAddress = 1;
}
```

이제 테스트를 진행해보면 모두 통과하는 것을 볼 수 있습니다. 

```
[==========] Running 2 test(s).
CMocka setup
[ RUN      ] LedsOffAfterCreate
[       OK ] LedsOffAfterCreate
[ RUN      ] TurnOnLedOne
[       OK ] TurnOnLedOne
CMocka teardown
[==========] 2 test(s) run.
[  PASSED  ] 2 test(s).
```

# 구현이 잘못 되었다!

많은 개발자들이 하드코딩, 그것도 명확히 문제가 있는 코드를 보면 마음이 편치 않습니다. 
최종 구현은 최하위 비트만 설정해야 합니다. 
지금 테스트만 보았을 때는 구현이 의도대로는 되었습니다. 
만약 TDD를 하고 있지 않다면 이 코드는 그대로 남겨둘 수 있습니다. 
그렇게 되면 나중에 버그가 발견될 수 있습니다. 

테스트 목록을 진행하면서 이러한 구현은 남겨지지 않게 될것읍니다. 
만약 하드코딩을 했는데 해당 부분에 대한 테스트가 목록에 없다면 당장 추가해야 합니다. 

# 테스트가 정답!

위에서 작성한 테스트는 1번 LED를 켜는 테스트를 하였습니다. 
현 시점에서 테스트는 통과하였습니다. 
하지만 다른 LED를 켜게 되면 해당 테스트는 실패하게 됩니다. 
그러면 이전에 작성한 하드코딩한 코드는 바로 수정하게 될 것입니다. 

테스트에서 필요하기 전에 코드를 추가하면 복잡성이 높아집니다. 
TDD는 개발 전에 올바른 테스트를 먼저 작성해야 합니다. 
올바른 테스트가 만든 뒤에야 코드를 작성해야 합니다. 

# 다음 테스트 선택하기 

현재 개발중인 LED 드라이버에서 큰 그림을 얻을 수 있도록 인터페이스를 발전해야 합니다. 
먼저 이전 테스트에서 LED를 끄는 테스트를 추가해보겠습니다. 
켜기와 끄는 기능은 상호보완적 관계이며, 추후 LED 조작이 서로 간섭되지 않는것을 검증할 수 있습니다. 

```c
void TurnOnLedOne(void ** state) {
  uint16_t virtualLeds = 0xffff;
  LedDriver_Create(&virtualLeds);
  LedDriver_TurnOn(1);
  LedDriver_TurnOff(1);
  assert_int_equal(0, virtualLeds);
}
```

이후 테스트가 통과할 수 있는 코드를 작성합니다. 

```c
void LedDriver_TurnOff(int ledNumber)
{
  *ledsAddress = 0;
}
```

이후 테스트를 실행하면 모든 테스트가 통과됩니다. 

```
[==========] Running 2 test(s).
CMocka setup
[ RUN      ] LedsOffAfterCreate
[       OK ] LedsOffAfterCreate
[ RUN      ] TurnOnLedOne
[       OK ] TurnOnLedOne
CMocka teardown
[==========] 2 test(s) run.
[  PASSED  ] 2 test(s).

```

# 구현이 잘못 되었다!

많은 개발자들이 하드코딩, 그것도 명확히 문제가 있는 코드를 보면 마음이 편치 않습니다. 
최종 구현은 최하위 비트만 설정해야 합니다. 
지금 테스트만 보았을 때는 구현이 의도대로는 되었습니다. 
만약 TDD를 하고 있지 않다면 이 코드는 그대로 남겨둘 수 있습니다. 
그렇게 되면 나중에 버그가 발견될 수 있습니다. 

테스트 목록을 진행하면서 이러한 구현은 남겨지지 않게 될것읍니다. 
만약 하드코딩을 했는데 해당 부분에 대한 테스트가 목록에 없다면 당장 추가해야 합니다. 

# 테스트가 정답!

위에서 작성한 테스트는 1번 LED를 켜는 테스트를 하였습니다. 
현 시점에서 테스트는 통과하였습니다. 
하지만 다른 LED를 켜게 되면 해당 테스트는 실패하게 됩니다. 
그러면 이전에 작성한 하드코딩한 코드는 바로 수정하게 될 것입니다. 

테스트에서 필요하기 전에 코드를 추가하면 복잡성이 높아집니다. 
TDD는 개발 전에 올바른 테스트를 먼저 작성해야 합니다. 
올바른 테스트가 만든 뒤에야 코드를 작성해야 합니다. 
