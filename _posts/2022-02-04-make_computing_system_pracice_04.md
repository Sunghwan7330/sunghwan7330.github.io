---
title: "[밑바닥부터 만드는 컴퓨팅] 프로젝트04 어셈블리"
excerpt: "해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템'  프로젝트04  부분을 정리하였습니다. "

categories:
  - 'make_computing_system'
tags:
  - 밑바닥부터 만드는 컴퓨팅

toc: true
toc_stic표ky: true

date: 2022-02-04
last_modified_at: 2022-02-04
---

# 개요 

해당 내용은 '밑바닥부터 만드는 컴퓨팅 시스템' 책의 프로젝트 04 의 내용을 정리하려 합니다. 
혹시나 따라하실 분은 실습준비 포스팅을 참고해서 프로그램을 다운받아주시면 됩니다. 

# 프로그램 실행 

다운받은 프로그램에서 `Assembler.bat` 를 실행해줍니다. 

![image](https://user-images.githubusercontent.com/35713051/152643376-f2de5a20-20d3-4484-bd75-90534f3dbe6d.png)

왼쪽의 문서 버튼을 눌러 파일을 열 수 있고, 그 옆에 화살표 버튼을 눌러 어셈블리를 실행할 수 있습니다. 

작성한 asm 파일을 연 뒤 실행 버튼을 누르면 프로그램에서 어셈블리를 읽어 우측에 기계어로 출력합니다. 
출력된 기계어는 hack 파일로 저장됩니다. 

이제 `CPUEmulator.bat` 를 실행해줍니다. 

![image](https://user-images.githubusercontent.com/35713051/152643406-06c47026-5bb8-4989-87d2-68cf74fd387a.png)

왼쪽의 문서 버튼을 눌러 파일을 열고 , 그 옆에 화살표 버튼을 눌러 어셈블리를 실행할 수 있습니다. 

이전에 생성된 hack 파일과 스크립트를 읽은 뒤 프로그램을 실행버튼을 누르면 프로그램이 수행됩니다. 
프로그램이 다 실행되면 out 파일이 생성되며, 이를 cmp 파일과 비교하여 프로그램이 정상적인지 확인할 수 있습니다. 


# multi 어셈블리 작성 

multi 는 곱셈을 수행하는 연산입니다. 

`Mult.asm` 파일을 열어 곱셈 연산을 수행하는 어셈블리를 작성하면 됩니다. 
파일을 열어 상단에 작성된 주석을 확인해보도록 하겠습니다. 

```
Multiplies R0 and R1 and stores the result in R2.
(R0, R1, R2 refer to RAM[0], RAM[1], and RAM[2], respectively.)

This program only needs to handle arguments that satisfy
R0 >= 0, R1 >= 0, and R0*R1 < 32768.

----------------------------------------------------------------

R0과 R1을 곱하고, 그 결과를 R2 에 저장합니다. 
(R0, R1, R2는 각각 RAM[0], RAM[1], RAM[2]를 나타냅니다.)

이 프로그램은 다음을 만족하는 인수를 처리하면 됩니다. 
R0 >= 0, R1 >= 0 그리고 R0 * R1 < 32768
```

주석을 확인해보면 R0과 R1메모리를 읽어 두 값을 곱한 뒤 R2 메모리에 저장하면 됩니다. 
작성한 어셈블리는 아래와 같습니다. 

```
@R2
M=0           // R2=0 

(LOOP)
    D=M[R1]

    @END      // if(R1==0)
    D;JEQ     //     goto END

    @R0
    D=M       // D=R0

    @R2
    M=M+D     // R2=R2+R0

    @R1
    M=M-1     // R1--

    @LOOP
    0;JMP     // goto (LOOP)

(END)
```

저도 어셈블리가 친숙하지는 않지만, 한줄한줄씩 보도록 하겠습니다. 

```
@R2
M=0           // R2=0 
```

`@R2` 를 작성하면 `M` 이 `R2` 의 메모리 주소로 변경됩니다.
이후 `M=0`을 작성하면 `R2`의 메모리에 값을 0으로 저장합니다. 

다음에 보이는 `(LOOP)` 와 `(END)` 는 jump 하기 위한 메모리 주소를 가리킵니다. 
C언어의 goto문과 유사하다고 보시면 됩니다. 

`D=M[R1]` 은 `D`레지스터에 `R1`의 값을 저장하게 됩니다. 

```
    @END      // if(R1==0)
    D;JEQ     //     goto END
```
이 구문은 조건을 만족하였을 때 주소를 jump 하게 되는 코드입니다. 
`D`의 값이 0인 경우 jump를 하게 되는데, jump하는 위치는 윗 라인에서 작성한 `(END)`입니다. 
위에서 `D`레지스터에 `R1`의 값을 셋팅하였으니, 이는 즉 `R1`의 값이 0이 되면 `(END)`로 jump하여 프로그램이 종료하게 됩니다. 

```
    @R0
    D=M       // D=R0
```

`@R0` 을 입력하면 `M` 이 `R0`의 메모리 주소로 변경됩니다. 
이후 `D=M` 을 입력하면 `D`레지스터에 `R0` 메모리의 값이 셋팅됩니다. 

```
    @R2
    M=M+D     // R2=R2+R0
```

`@R2` 를 입력하면 `M`이 `R2` 의 주소로 변경합니다. 
`M`에 `R2`가 셋팅되어있고, 위에서 `D`레지스터에는 R0의 값을 입력하였습니다. 
따라서 `M=M+D` 를 입력하면 `R2 = R2 + R1` 이 수행되게 됩니다. 

```
    @R1
    M=M-1     // R1--
```
`@R1` 을 입력하면 `M`이 `R1`의 메모리 주소로 변경됩니다. 
이후 `M=M-1` 을 입력하면 `R1` 메모리의 값에서 1을 빼어 다시 `R1` 메모리에 저장합니다. 

```
    @LOOP
    0;JMP     // goto (LOOP)
```

이 구문은 loop로 jump 하는 로직입니다. 
조건에 관계없이 무조건 loop로 이동합니다. 

위를 다시 보면 `R1` 이 0이 될때까지 `R2`에 `R0`의 값을 더하고 `R1` 의 값을 1씩뺍니다. 
즉, `R0`을 `R1` 번 더하게 됩니다. 
C 코드로 작성하면 아래와 같게 됩니다. 

```c
r2 = 0;

while(true) {
  if (r1 == 0)
    break;
  r2 = r2 + r0;
  r1 = r1 - 1;
}
```

더 심플하게 작성할 수도 있지만 최대한 위의 어셈블리와 비슷하게 작성해보았습니다. 
참고용으로 봐주시면 되겠습니다. 

# fill 어셈블리 작성 

다음으로 fill.asm 파일을 열어 어셈블리를 작성하시면 됩니다. 
이번에도 역시 파일을 열어 작성되어 있는 주석 메시지를 확인해보도록 하겠습니다. (번역기의 힘을 빌려...!)

```
Runs an infinite loop that listens to the keyboard input.
When a key is pressed (any key), the program blackens the screen,
i.e. writes "black" in every pixel;
the screen should remain fully black as long as the key is pressed. 
When no key is pressed, the program clears the screen, i.e. writes
"white" in every pixel;
the screen should remain fully clear as long as no key is pressed.

------------------------------------------------------------------

키보드 입력을 수신하는 무한 루프를 실행합니다. 
키를 누르면 (아무 키나) 프로그램의 화면 을 검게, 즉 모든 픽셀을 검정색으로 씁니다. 
키를 누르고 있는 동안은 완전히 검은색으로 유지되어야 합니다. 
아무 키도 누르지 않으면 프로그램이 화면을 깨끗하게, 즉 모든 픽셀을 흰색으로 씁니다. 
아무키도 누르지 않는 한 화면은 완전히 깨끗한 상태를 유지해야 합니다. 
```

이번 프로그램은 무한루프를 반복하는 프로그램입니다. 
키보드에 값이 입력되었다면 모든 픽셀의 값을 검정색으로 쓰고, 값이 입력되지 않았다면 흰색 값으로 쓰면 됩니다. 

전체 코드는 다음과 같습니다. 

```
@color         // declare color variable
M=0            // by default is white

(loop)
    @SCREEN    // RAM address 16384
    D=A

    @pixels 
    M=D       // pixel address (starting point: 16384, max: 16384+8192=24576)

    @KBD      // D = ascii code of a keyboard input
    D=M

    @black
    D;JGT     // if(keyboard > 0) goto black

    @color
    M=0       // otherwise white
    
    @color_screen
    0;JMP     // jump to subroutine that actually changes the color of screen

    (black)
        @color
        M=-1  // set to black

    (color_screen)
        @color
        D=M
        @pixels
        A=M
        M=D   // M[pixels] = @color

        @pixels
        M=M+1
        D=M

        @24576 // loop until end of pixels
        D=D-A
        @color_screen
        D;JLT

@loop
0;JMP         // infinite loop
```

이번에도 라인 단위로 살펴보도록 하겠습니다. 

```
@color         // declare color variable
M=0            // by default is white
```

`@color` 입력하여 메모리 주소를 이동한 메모리 위치의 값을 0으로 설정합니다. 

다음의 `@LOOP` 는 jump하기 위한 주소를 가르킨다고 보시면 됩니다. 

```
    @SCREEN    // RAM address 16384
    D=A
	
	@pixels 
    M=D       // pixel address (starting point: 16384, max: 16384+8192=24576)
```

먼저 `@SCREEN` 의 주소를 `D`레지스터에 입력합니다. 
이후 `@pixels`의 메모리 주소에 `D`레지스터의 값을 입력합니다. 
이렇게 되면 `@pixels` 에 메모리에 `@SEREEN` 의 주소값이 저장되게 됩니다. 

```
    @KBD      // D = ascii code of a keyboard input
    D=M
	
    @black
    D;JGT     // if(keyboard > 0) goto black
```

`@KBD`는 키보드 입력 값이 기록되는 메모리 주소입니다. 
키보드에 입력된 값을 `D` 레지스터에 저장합니다. 

이후 `D` 의 값이 0이라면 `(black)` 부분으로 jump를 합니다. 
키보드의 입력이 없다면 메모리 주소에 0이 입력되고, 아니라면 키보드의 아스키코드 값이 입력됩니다. 
즉, D의 값이 0이 아니라면  `(black)` 부분으로 jump를 하고, 아니라면 아래로 이동하게 됩니다. 

```
    @color
    M=0       // otherwise white
    
    @color_screen
    0;JMP     // jump to subroutine that actually changes the color of screen
```
이 전 확인한 코드에서 키보드 입력이 없었다면 jump를 하지 않기 때문에 위에 보이는 코드로 이동하게 됩니다. 
`@color` 부분의 메모리 값을 0으로 변경한 뒤 `@color_screen` 의 주소로 jump 하게 됩니다. 

```
    (black)
        @color
        M=-1  // set to black
```




(이어서 계속..)