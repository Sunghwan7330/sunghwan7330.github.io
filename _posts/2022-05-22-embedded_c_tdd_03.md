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
