---
title: "전자서명에 대한 전반적인 이해"
excerpt: "전자서명이 무엇인지, 어떻게 서명이 이뤄지며, 어떻게 사용되는지 알아보려 합니다."

categories:
  - 'infomation'

date: 2021-06-28
last_modified_at: 2021-07-04
---

# 개요

Naver D2에서 인증서에 관련된 글을 보았습니다. 

해당 글을 읽고 정리하기 위해 포스팅을 하게 되었습니다. 

Naver D2의 글은 네이버 어플리케이션의 전자서명 원리를 기반으로 설명하고 있습니다. 

본 글에서는 어플리케이션에서의 전자서명이 아닌 좀 더 넓은 측면에서 간단히 이해하기 쉽도록 설명해보려 합니다. 

# 전자서명이란?

위키피디아에서는 전자서명을 아래와 같이 서술하였습니다. 

```
전자서명이라 함은, 서명자를 확인하고 서명자가 당해 전자문서에 서명했다는 사실을 나타내는 데 이용하려고, 특정 전자문서에 첨부되거나 논리적으로 결합된 전자적 형태의 정보를 말한다.
```

조금 더 간단히 생각해보자면 '전자'는 잠시 잊고 '서명' 만 생각해보도록 합시다. 
일반적으로 서명은 자신만 만들어낼 수 있는 문자(?)를 나타냅니다. 
서류와 같은 곳에 자신임을 인증하기 위해 이름 옆에 서명을 하곤 합니다. 

이제 다시 '전자'를 붙여보겠습니다. 

말 그대로 자신만이 나타낼 수 있는 문자.... 가 아니고 값을 제출하여 자신임을 증명하는 방법입니다. 

어떻게 전자상에서 자신을 증명할 수 있는 값을 만들어낼까요? 

전자서명을 위한 기술을 알아보도록 하겠습니다. 

# 전자 서명의 기술 기반

전자 서명을 이해하기 위한 기반이 되는 기술을 이해할 필요가 있습니다. 
해시 함수와 공개키 알고리즘이 중요한 핵심 기술이라고 할 수 있습니다. 
이 두가지 기술에 대해 간단히 알아보겠습니다. 

## 해시함수

해시 함수는 0과 1로 조합된 길게 이루어진 바이너리를 고유한 값으로 축약하는 함수입니다. 
축약하여 나온 결과값을 해시값이라고 부르게 됩니다. 

긴 바이너리가 짧아지기 때문에 축약되는 과정에서 데이터의 손실이 발생합니다. 
그렇기때문에 축약된 데이터를 원본 데이터로 변경하는것이 불가능합니다. 

또한 값을 조금만 바꾸어도 해시값이 완전히 다른 값이 나타난다는 특징이 있습니다. 

```
pi@raspberrypi:~/work $ echo 0123456789 > aaa
pi@raspberrypi:~/work $ md5sum aaa
3749f52bb326ae96782b42dc0a97b4c1  aaa
pi@raspberrypi:~/work $ echo 0123456788 > bbb
pi@raspberrypi:~/work $ md5sum bbb
80f11ae13a48c1e74537a72aa445b399  bbb
pi@raspberrypi:~/work $
```

'0123456789' 와 '0123456788' 을 보면 끝의 한글자만 다릅니다. 
바이너리로 보자면 8은 0x38이고 9는 0x39로 1비트 차이입니다. 
즉 '0123456789' 와 '0123456788' 는 바이너리로 비교하자면 단 1비트만 다른 값입니다. 

하지만 해시값은 전혀 다른 결과가 나옵니다. 
이처럼 해시함수는 조금만 바뀌어도 전혀 다른 해시값을 나타나는 특징이 있습니다. 

대표적인 해시함수 알고리즘으로는 MD5, SHA(SHA1, SHA2, SHA256 등)가 있습니다.

MD5는 128비트 해시값을 반환하는 해시함수이며, SHA 는 종류에 따라 각각 160, 224, 256, 384, 512 비트 해시값을 반환하는 해시함수이다. 

## 공개키 암호화 

암호화 알고리즘은 데이터를 알아볼 수 없도록 변경하는 알고리즘입니다. 
암호화를 하게 되면 해시 알고리즘처럼 전혀 알아볼 수 없는 형태로 바뀌며, 데이터의 일부만 바뀌어도 암호화 결과가 모두 바뀐다는 특징이 있습니다. 
해시 알고리즘과의 다른점이라면 축약하지 않기 때문에 데이터의 길이가 변경되지 않습니다. 
또한 데이터 길이가 변경되지 않기 때문에 원래의 데이터로 복원하는것이 가능합니다.(복호화)
암호화 알고리즘에 대해서는 기회가 되면 좀 더 자세히 다뤄보도록 하겠습니다. 

컴퓨터에서 사용하는 암호화 알고리즘은 크게 두가지로 구분됩니다. 

* 공개키 암호화 알고리즘
* 대칭키 암호화 알고리즘

암호화 알고리즘은 키를 이용하여 데이터를 암호화 및 복호화를 하게 됩니다. 
위의 두 알고리즘은 키의 형태에 따라 나눠지게 됩니다. 

대칭키 알고리즘은 암호화할때와 복호화할때 사용하는 키가 같은 알고리즘입니다. 
공개키 알고리즘은 암호화할때와 복호화할때 사용하는 키가 다른 알고리즘입니다. 

본 포스팅에서는 대칭키 알고리즘에 대해 간단히 알아보고 넘어가겠습니다. (나중에 자세히 알아볼께요!)
즉 키가 2개가 사용되는 알고리즘입니다. 이 두개의 키는 키쌍이라고 부르게 됩니다. 
이 중 하나는 공개키로써 누구나 알 수 있도록 공개하여 사용하며, 다른 하나는 개인키로써 자신만 알 수 있도록 사용하게 됩니다. 

공개키로 데이터를 암호화한다면 개인키로만 복호화를 할 수 있습니다. 
즉 데이터는 누구나 암호화할 수 있지만, 이 암호화 데이터는 자신만 복호화할 수 있게 됩니다.

반대로 개인키로 데이터를 암호화한다면 공개키로 복호화할 수 있습니다. 
자신이 암호화한 데이터는 누구나 복호화할 수 있다는 의미입니다. 

누구나 복호화할 수 있는 암호화 방법을 왜 사용하는걸까요?
개인키로 데이터를 하는 이러한 방식은 자신을 인증할 때 사용하게 됩니다. 

> 당신이 맞다는 것을 증명하고 싶다면 이 데이터를 당신의 개인키로 암호화해보세요.

자신의 개인키로 해당 데이터를 암호화하여 상대에게 보냅니다. 
그러면 상대는 그 사람의 공개키로 복호화를 수행합니다. 
만약 복호화 결과가 원본 데이터와 일치한다면 그 사람이 맞다는 것을 입증할 수 있습니다. 
해당 데이터는 상대의 개인키로만 암호화할 수 있고, 개인키는 상대만 알고있기 때문입니다. 
만약 상대가 아니라면 개인키를 모르기 때문에 공개키로 복호화 시 원본데이터가 아닌 다른 결과가 나타나기 때문입니다.

이와 같은 방법으로 공개키 알고리즘을 사용하여 상대를 확인하는 방법을 전자서명 알고리즘에서 사용합니다.

# 전자서명을 이용한 어플리케이션 배포 

## 무결성 확인

무결성이란 메시지 또는 데이터를 전송할 때 데이터가 변조되지 않은것을 의미합니다.
이때 사용하는 기술이 바로 해시함수입니다. 

많은 사이트에서 파일 다운로드 기능을 제공할 때 파일 링크와 함께 md5나 sha 해시값을 같이 적어놓은것을 볼 수 있습니다. 
이 해시값은 무결성을 확인하기 위하 사용됩니다.

웹사이트에서 약 10GB의 파일을 다운받았다고 가정해봅시다.
이 데이터가 전송되는 과정에서 변조가 되어 악성코드가 들어있거나 혹은 임의의 값으로 변경되었는지 어떻게 확인할 수 있을까요?
데이터가 바뀌지 않았는지 처음부터 끝까지 전부 확인해야할까요? 
10GB 란 크기는 작지않은 크기입니다. 
다운받는데도 오래걸리는데, 이를 처음부터 비교하는데도 오래걸리겠죠.

이때 MD5나 SHA 해시값을 통해 무결성을 확인할 수 있습니다. 
데이터를 정상적으로 수신하였다면 파일의 해시값이 배포한 사이트에서 제공하는 해시값과 같은 값게됩니다. 
만약 데이터가 변조되었다면 해시값이 다르게 됩니다. 
위에서 알아본 바와 같이 해시함수는 데이터의 일부만 바뀌어도 해시값의 전부가 바뀌기 때문입니다.

## 진보된 무결성 확인

위의 방법으로 파일의 무결성을 확인할 수 있습니다. 
하지만 배포하는 사이트를 해킹해서 파일의 해시값마저 해킹하였다면 어떻게 될까요?
배포하는 파일을 변조할 수 있는 해커라면 사이트의 해시값을 바꾸는것도 불가능하지 않을것으로 생각됩니다. 

이러한 위조를 방지하기 위해서 전자서명 기술이 필요하게 됩니다. 
위에서 알아본 바와 같이 전자서명은 자신만 할 수 있으며, 서명한 사람의 공개키를 이용하여 본인을 확인할 수 있습니다. 
이러한 전자서명 방법을 이용하여 무결성을 확인하는 방법은 다음과 같습니다. 

1. 파일의 해시값을 배포자의 개인키로 암호화 (전자서명)
1. 배포자는 파일과 전자서명을 배포함
1. 다운로더는 파일과 전자서명을 다운로드함 
1. 다운로드 후 배포자의 공개키로 전자서명을 복호화하여 파일의 해시값과 비교

위와 같은 방법으로 무결성을 확인하면 더 안전합니다. 
해커가 파일은 변조할 수 있더라도 전자서명을 위조할수는 없습니다. 
전자서명을 하기 위해서는 배포자의 개인키가 필요하기 때문입니다. 
다른 개인키로 암호화한다면 배포자의 공개키로 복호화할때 전혀 다른 결과가 나타나기 때문에 전자서명을 위조하기는 어렵습니다. 

그럼 배포자의 공개키를 바꾸면 되지 않을까요?
그래서 등장한 것이 바로 인증서입니다. 
인증서는 신뢰할 수 있는 인증기관에서 발급해주는 데이터입니다. 
인증서 안에는 공개키를 포함한 자신을 인증하기 위한 다양한 정보들이 있습니다. 
한때 인터넷뱅킹과 같은 곳에서 우리를 고통받게 했던 공인인증서 역시 이와 같은 인증서입니다. 

아무리 뛰어난 해커라고 해도 인증기관의 인증서를 조작하기는 어렵습니다. 
그렇기 때문에 지금까지도 이러한 인증서를 이용한 인증방식을 계속 사용해오고 있습니다. 

# 공인인증서 

공인인증서의 개념을 간략하게 설명하자면 공개키 알고리즘의 공개키를 파일형태로 만들어 놓은 것이라고 할 수 있습니다. 
공인인증서는 공개키를 공유하기 위한 파일이며, 공개키 외에도 다양한 정보가 포함되어 있다. 
발행 주체, 주체의 공개키, 발급자, 발급자의 서명 등이 인증서에 포함되어 있다. 

공개키 알고리즘을 이용하여 키쌍을 생성합니다. 이때 키를 생성한 주체는 공인 인증 기관에 공개키와 자신의 정보를 등록합니다. 
이때 공인 인증 기관은 주체자에게 인증서를 발급해주는데, 이 인증세에서는 주체에 대한 정보와 공개키, 그리고 인증 기관을 나타내는 발급자의 정보와 서명 정보가 기록되어 있습니다. 
발급자의 서명이란 인증서에 기록된 여러 정보를 하나로 모은 후 해시 함수에 입력하여 해시값을 얻은 후 발급자의 개인키로 암호화한 결과를 말합니다. 
발급자는 인증서를 요청하는 기업이나 개인에 대한 철저하게 신원을 확인합니다. 
그렇기 때문에 제 3자가 사칭하여 인증서를 발급받는 것은 어렵습니다. 

그럼 이제 다시 인증서를 이용하여 파일의 무결성 여부를 확인하여 봅시다. 

1. 공인 인증 기관의 공개키로 인증서에 포함된 서명을 복호화하여 인증서의 변조 여부를 확인함
1. 인증서에 포함된 주체의 공개키를 이용하여 파일의 서명을 복호화하여 변조 여부를 확인함

이렇게 공인인증서를 이용한다면 아무리 뛰어난 해커라고 해도 서명을 위조하는 것은 매우 어려운 일입니다. 

# 정리 

위조 방지를 위한 전자서명에 대하여 전반적으로 알아보았습니다. 

* 해시 함수 
* 공개키 암호화
* 해시함수와 공개키 알고리즘을 이용한 전자서명 방법
* 진보된 무결성 확인을 위한 공인인증서 

간략하게 설명드렸지만, 대략적인 흐름이 이해되셨기를 바랍니다. 
그러면 왜 은행권이나 공공기관에서 공인인증서를 사용하라고 하는지 이해는 되실껍니다. 
하지만 여기서 제일 중요한 점은 개인키입니다. 
개인키가 노출되지 않게 주의하는것이 가장 중요하기 때문이죠. 

조금 더 자세한 정보를 원하신다면 원본의 글을 읽어보시는것을 추천드립니다. 
원문은 아래의 '참고' 에 있는 링크를 통해 확인하실 수 있습니다. 

# 참고 

* https://d2.naver.com/helloworld/744920