---
title: "[FastAPI] ORM에 대한 소개"
excerpt: "ORM 의 소개와 FastAPI 에서 ORM 사용의 예시를 설명합니다."

categories:
  - 'fast_api'
tags:
  - FastAPI
  - ORM

date: 2025-07-13
last_modified_at: 2025-07-13
---


# ORM(Object-Relational Mapping)이란?

* ORM은 Object-Relational Mapping의 약자
* 객체 지향 프로그래밍 언어의 객체(Object)와 관계형 데이터베이스의 테이블(Table) 간의 자동 매핑을 제공
* 코드에서 직접 SQL을 작성하지 않고도 DB 조작이 가능

## ORM의 핵심 개념

* 객체 <-> 테이블
* 클래스는 테이블에 대응
* 객체 인스턴스는 테이블의 행(Row)에 대응
* 클래스의 속성은 테이블의 컬럼(Column)에 대응

# SQLAlchemy

## SQLAlchemy 특징

* 파이썬 ORM 라이브러리 중 가장 널리 사용됨
* SQL을 추상화하여 코드로 데이터베이스 제어 가능
* ORM + SQL Expression Language 지원 (하이브리드 형태)
* 다양한 DBMS 지원: MySQL, PostgreSQL, SQLite, 등

## 주요 구성 요소
- `Engine`: DB 연결 객체
- `Session`: 트랜잭션 관리 및 ORM 조작의 핵심
- `Base`: 모든 모델 클래스의 부모 클래스
- `Model`: 실제 DB 테이블에 대응되는 클래스


# 예시 

## ORM을 사용하지 않았을 때의 예

```python
# 사용자 정의 클래스
import mysql.connector
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

# Pydantic 모델
class UserIn(BaseModel):
    name: str
    email: str

class UserOut(UserIn):
    id: int

# MySQL 연결
conn = mysql.connector.connect(
    host="localhost",
    user="user",
    password="passwd",
    database="test"
)
cursor = conn.cursor()

# POST: 사용자 추가
@app.post("/users/", response_model=UserOut)
def create_user(user: UserIn):
    cursor.execute("INSERT INTO users (name, email) VALUES (%s, %s)", (user.name, user.email))
    conn.commit()
    user_id = cursor.lastrowid
    return {"id": user_id, "name": user.name, "email": user.email}

# GET: 사용자 목록 조회
@app.get("/users/", response_model=list[UserOut])
def get_users():
    cursor.execute("SELECT id, name, email FROM users")
    rows = cursor.fetchall()
    return [{"id": r[0], "name": r[1], "email": r[2]} for r in rows]

```

## ORM 을 사용하였을 때의 예시

```python
from fastapi import FastAPI, Depends
from pydantic import BaseModel
from sqlalchemy import Column, Integer, String, create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker, Session

app = FastAPI()

# MySQL 연결 URL
SQLALCHEMY_DATABASE_URL = (
    "mysql+pymysql://user:passwd@localhost:3306/test"
)

# 엔진 및 세션 설정
engine = create_engine(SQLALCHEMY_DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()

# ORM 모델
class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String(100))
    email = Column(String(100))


# Pydantic 모델
class UserIn(BaseModel):
    name: str
    email: str

class UserOut(UserIn):
    id: int

# 세션 의존성
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

# POST: 사용자 추가
@app.post("/users/", response_model=UserOut)
def create_user(user: UserIn, db: Session = Depends(get_db)):
    db_user = User(name=user.name, email=user.email)
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user

# GET: 사용자 목록 조회
@app.get("/users/", response_model=list[UserOut])
def get_users(db: Session = Depends(get_db)):
    return db.query(User).all()

```

## ORM 의 장점
* 생산성 향상
  * SQL 없이 코드만으로 CRUD 처리 가능
* 가독성 및 유지보수 용이
  * SQL보다 더 직관적인 코드 작성 가능
* 보안성 강화
  * ORM이 SQL 인젝션을 자동 방어 (프레임워크에 따라 다름)
* DBMS 추상화
  * 코드 변경 없이 DBMS 교체 가능 (예: MySQL ↔ PostgreSQL)
* 트랜잭션 관리 용이
  * ORM이 내부적으로 트랜잭션 처리 지원

## ORM 의 단점

* 퍼포먼스 이슈
  * 복잡한 쿼리에서 자동 생성 SQL의 성능이 낮을 수 있음

* 학습 곡선
  * ORM 문법과 개념을 따로 배워야 함

* 복잡한 쿼리 처리 한계
  * JOIN, 집계, 서브쿼리 등은 ORM만으로 처리 어려움
  * 결국 일부는 직접 SQL 작성 필요
