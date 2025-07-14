---
title: "[FastAPI] MySQL Geometry 소개"
excerpt: "MySQL 에서 Geomeyty 와 FastAPI에서 사용 예시를 소개합니다."

categories:
  - 'fast_api'
tags:
  - FastAPI
  - Geometry

date: 2025-07-13
last_modified_at: 2025-07-13
---


# MySQL Geometry 타입

## 개요
- 공간 데이터를 위한 MySQL의 내장 타입
- `POINT`, `LINESTRING`, `POLYGON` 등 다양한 형식 지원
- WKT(WELL-KNOWN TEXT), WKB(WELL-KNOWN BINARY) 형식 사용 가능
- 공간 인덱스(SPATIAL INDEX)로 성능 최적화 가능

## 주요 하위 타입
- `POINT`: 단일 좌표값 (x, y 또는 경도, 위도)
- `LINESTRING`: 여러 좌표를 잇는 선
- `POLYGON`: 닫힌 도형(다각형)
- `MULTIPOINT`, `MULTILINESTRING`, `MULTIPOLYGON`: 복수의 공간 객체
- `GEOMETRYCOLLECTION`: 다양한 지오메트리 객체의 집합

## 주요 공간 함수
- `ST_GeomFromText(text)`: WKT를 Geometry로 변환
- `ST_AsText(geom)`: Geometry를 WKT 문자열로 반환
- `ST_Distance(g1, g2)`: 두 지오메트리 간 거리 반환
- `ST_Contains(g1, g2)`: g1이 g2를 포함하는지 여부
- `ST_Within(g1, g2)`: g1이 g2 내부에 있는지 여부
- `ST_Intersects(g1, g2)`: 두 지오메트리가 겹치는지 여부


# Python Shapely 라이브러리

## 개요
- Python에서 2D 지오메트리 연산을 수행하는 대표적인 라이브러리
- 포맷 간 변환, 도형 생성, 공간 연산 등 지원
- GIS 라이브러리(GeoPandas, GDAL 등)와도 연동 가능

## 지원 객체
- `Point(x, y)`: 단일 좌표
- `LineString([...])`: 선형 도형
- `Polygon([...])`: 닫힌 다각형
- `MultiPoint`, `MultiPolygon`, `GeometryCollection` 등 확장 지원

## 포맷 변환 기능
- `wkt.loads(text)`: WKT 문자열 → 지오메트리 객체
- `wkt.dumps(geometry)`: 지오메트리 객체 → WKT 문자열
- `loads` 와 `dumps` 는 WKB 바이너리 포맷도 지원

## 주요 공간 메서드
- `.distance(other)`: 두 객체 간 거리 계산
- `.contains(other)`: 객체가 다른 객체 포함 여부 판단
- `.within(other)`: 객체가 다른 객체 내부인지 판단
- `.intersects(other)`: 두 객체가 교차하는지 판단

# FastAPI 에서의 사용 예시 

* 예시는 main 부분만 작성하였으며 전체 코드는 아래의 저장소에서 확인
  * https://github.com/Sunghwan7330/my_study/tree/master/python/fastapi/geometory_test

* DB 스키마 
```
CREATE TABLE places (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(50),
  location POINT NOT NULL,
  SPATIAL INDEX(location)
);
```

* FastAPI 예시 코드
```python
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session
from sqlalchemy import func
from shapely.wkt import loads as load_wkt
from shapely.geometry import Point

from database import SessionLocal, engine
from models import Base, Place
from schemas import PlaceCreate, PlaceOut

Base.metadata.create_all(bind=engine)

app = FastAPI()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.post("/places/", response_model=PlaceOut)
def create_place(place: PlaceCreate, db: Session = Depends(get_db)):
    wkt = f"POINT({place.longitude} {place.latitude})"
    
    new_place = Place(
        name=place.name,
        location=func.ST_GeomFromText(wkt)
    )
    db.add(new_place)
    db.commit()
    db.refresh(new_place)

    point = load_wkt(wkt)
    return PlaceOut(
        id=new_place.id,
        name=new_place.name,
        latitude=point.y,
        longitude=point.x
    )

@app.get("/places/", response_model=list[PlaceOut])
def get_places(db: Session = Depends(get_db)):
    places = db.query(
        Place.id,
        Place.name,
        func.ST_AsText(Place.location).label("location_wkt")
    ).all()

    results = []
    for p in places:
        point = load_wkt(p.location_wkt)
        results.append({
            "id": p.id,
            "name": p.name,
            "latitude": point.y,
            "longitude": point.x
        })

    return results

@app.put("/places/{place_id}", response_model=PlaceOut)
def update_place(place_id: int, new_data: PlaceCreate, db: Session = Depends(get_db)):
    place = db.query(Place).filter(Place.id == place_id).first()
    if not place:
        raise HTTPException(status_code=404, detail="Place not found")

    wkt = f"POINT({new_data.longitude} {new_data.latitude})"
    place.name = new_data.name
    place.location = func.ST_GeomFromText(wkt)
    db.commit()
    db.refresh(place)

    point = load_wkt(wkt)
    return PlaceOut(
        id=place.id,
        name=place.name,
        latitude=point.y,
        longitude=point.x
    )

@app.delete("/places/{place_id}")
def delete_place(place_id: int, db: Session = Depends(get_db)):
    place = db.query(Place).filter(Place.id == place_id).first()
    if not place:
        raise HTTPException(status_code=404, detail="Place not found")

    db.delete(place)
    db.commit()
    return {"message": "Place deleted"}
```
