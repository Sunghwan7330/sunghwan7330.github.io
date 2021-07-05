---
type: posts
time: "2015.03 ~ 2015.06"
title: "비콘 기반 전력관리 플랫폼"
skills: [C, BlueZ, BLE]
description: ""
image: ""
company: "Gigalane"
categories: [project]
---

## 기간

* 2015.03 ~ 2015.06

## 기술 

* C / RaspberryPI / WiringPI / BLE / Beacon / BlueZ

## 업무

* Raspberry Pi 기반 Socket 서버 구현
* Raspberry Pi를 이용한 센서 제어 모듈 구현 (온습도, 조도, Gyro, PIR 등)
* 센서 값 보정 및 정형화 작업 및 이상 감지 알람기능 구현 
* BLE Gatt 통신을 통한 Beacon 상태 제어 모듈 구현
* Beacon 기반의 제품 스케쥴링 기능 구현 

## 소개 

![image](https://user-images.githubusercontent.com/35713051/121361162-fe4dda80-c96f-11eb-9da1-cec89a313f4b.png)

* Smart Plug는 플러그에 Beacon이 내장되어 있는 형태이며, 소모되는 전력을 BLE로 전송하며, BLE GATT를 이용하여 플러그의 전원을 제어할 수 있음
* Master Beacon은 BLE 모듈을 이용하여 가정 내의 스마트 플러그의 소모 전력을 수신하거나 전원을 제어하고, Tem/Hum, CO/CO2, LNG/LPG, Gyro/Acc, PIR 센서를 이용하여 가정 내의 환경을 모니터링함
* Master Beacon Management Server은 Master Beacon으로부터 가정 내의 정보를 수신하여 센서 데이터를 확인하거나 Smart Plug의 소모 전력을 확인하고 전원을 원격으로 제어할 수 있고, Smart Plug의 스케줄이나 타이머를 설정할 수 있음

## 구현 결과 

![image](https://user-images.githubusercontent.com/35713051/121361270-19b8e580-c970-11eb-928d-3e5faea4cc9e.png)


