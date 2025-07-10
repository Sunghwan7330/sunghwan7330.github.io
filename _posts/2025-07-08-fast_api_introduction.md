---
title: "[FastAPI] 소개"
excerpt: "FastAPI 에 대한 소개와 설치 방법을 소개합니다."

categories:
  - 'fast_api'
tags:
  - FastAPI

date: 2025-07-08
last_modified_at: 2025-07-08
---

# 개요

웹 서비스를 이용하여 자료 제공을 위해 REST API 개발이 필요하게 되었습니다. 

간편하게 개발할 수 있는 방법을 찾던 중 FastAPI 라는 프레임워크를 알게 되어 간단하게 내용 정리를 하려 합니다. 

# FastAPI란?

* Python 기반의 프레임워크 (python 3.6 이상에서 지원)
* 비동기 기반의 웹 프레임워크
* 자동 문서화(Swagger, ReDoc) 제공
* Pydantic 기반의 데이터 검증과 직렬화 지원
* RESTful API 개발에 최적화되어 있으며, 고성능 API 서버를 손쉽게 구축할 수 있게 해줌

# FastAPI 의 주요 특징

| 특징               | 설명                                             |
| ---------------- | ---------------------------------------------- |
| **비동기 지원**       | `async/await`를 통해 높은 처리량과 빠른 응답을 제공            |
| **자동 문서화**       | Swagger UI, ReDoc 문서 자동 생성 (OpenAPI 3 기반)      |
| **타입 기반 데이터 검증** | Python 타입 힌트 + Pydantic 으로 Request/Response 검증 |
| **성능**           | Uvicorn 등 ASGI 서버와 함께 사용하면 성능이 매우 우수           |
| **간결한 코드**       | 선언적이고 간결한 코드 스타일                               |
| **모듈성 & 확장성**    | 의존성 주입(Dependency Injection) 지원                |

# 다른 파이썬 웹 프레임워크와 비교

| 항목          | FastAPI                 | Flask                 | Django REST Framework |
| ----------- | ----------------------- | --------------------- | --------------------- |
| **비동기 처리**  |  내장 지원 (ASGI 기반)       |  기본 지원 없음 (추가 모듈 필요) |  부분적, 복잡함 |
| **성능**      |  매우 빠름 (Node.js 수준)   | 중간                    | 느림                    |
| **타입 검증**   |  자동 (타입 힌트 + Pydantic) | 수동                  |  수동 또는 serializers   |
| **문서 자동화**  |  내장 Swagger/ReDoc      | 수동 구성               |  Swagger 제공 (설정 필요)  |
| **학습 곡선**   | 보통 (타입 힌트 필요)           | 매우 쉬움                 | 높음               |
| **생산성**     | 높음                      | 높음                    | 높음             |
| **프로젝트 구조** | 유연                      | 매우 유연                 | 정형화됨 (Django 스타일)     |

# 설치 방법

FastAPI 를 사용하기 위해서는 python 이 필요합니다. 

python 설치 과정은 생략하도록 하겠습니다. 

## pip를 이용하여 FastAPI 설치 

```
$ pip install fastapi
```

## Uvicorn 설치 (비동기 웹서버)


```
$ pip install fastapi uvicorn
```

# 코드 작성 및 실행
* main.py 파일 작성

```python
from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str, None] = None):
    return {"item_id": item_id, "q": q}
```

* uvicorn 을 이용하여 서버 실행
  
``` bash
$ uvicorn main:app --reload

```

* 동작 확인
  
```bash
$ curl http://127.0.0.1:8000
{"Hello":"World"}

$ curl http://127.0.0.1:8000/items/5?q=somequery
{"item_id":5,"q":"sonequery"}
```

# 정리

FastAPI 는 python 기반의 프레임워크로 REST API 개발에 특화되어 있습니다. 

개발이 쉽고 직관적이며, 특히 API 문서 자동화 기능이 좋아보입니다. 

다른 웹 서비스를 같이 수행해야 한다면 DJango 와 같은 프레임워크를 사용할 수 있겠지만, REST API 개발이 목적이라면 FastAPI가 좋은 선택이 될 수 있을 것 같습니다. 

# 참고 

* https://fastapi.tiangolo.com/ko/
