---
title: "2. oneM2M 애플리케이션 개발 절차 (1)"
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-02-19 03:19:18 +0900
categories: [IoT, oneM2M]
tags: [IoT, oneM2M]
math: true
mermaid: true
pin: false
---

## 원격 조명 제어 유스케이스 개요

---

원세종 군은 하루 일과를 마친 후 지친 몸을 이끌고 집으로 귀가했다. 집에 혼자 살고 있기 때문에 귀가했을 때 집 안에 있는 불은 모두 꺼져있는 상황이다. oneM2M 사물 인터넷 서비스를 사용하지 않았던 한달 전까지만 해도, 원세종 군은 귀가 후에 거실, 주방, 화장실 등의 불을 일일이 켜고, 취침 전에는 다시 모든 실내등들을 일일이 찾아다니며 꺼야만 했다. 그러나, 한달 전 oneM2M 기반 스마트홈 서비스에 가입하면서 부터 스마트폰을 이용한 사물인터넷 원격 조명 제어 서비스를 이용함으로써 집에 들어가서 손가락 하나로 집 안의 모든 조명을 원격으로 제어할 수 있게 된다.

위의 예시를 바탕으로 **요구사항을 도출**해보자.

- 조명은 집에 배치되어 있으며, Home Gateway에 연결되어 있다.
- 조명은 무선 접근 제어 인터페이스를 통하여 oneM2M Service Platform과 상호작용을 할 수 있다.
- Home Gateway는 조명을 검색 후 본인 Home Gateway에 연결한다.
- Home Gateway는 조명과 oneM2M Service Platform 사이에 위치하며, 상호간의 통신 기능을 제공한다.
- oneM2M Service Platform는 조명과 Home Gateway를 등록/검색/관리할 수 있다.
- oneM2M Service Platform는 조명을 그룹관리할 수 있다.
- oneM2M Service Platform는 알림 서비스 등 공통 서비스 기능(CSF)을 제공한다.
- Application Service는 스마트폰에 내장되어 있다.
- Application Service는 Home Gateway에 연결되어 있는 조명들을 검색할 수 있다.
- Application Service는 조명 상태를 변경시키는 명령어를 전송할 수 있다.
- Application Service는 조명 상태를 조회하는 명령어를 전송할 수 있다.

![img_1](/assets/img/2022-02-19/IoT/oneM2M application development procedure %281%29/Untitled.png)

## 아키텍처 설계

---

![img_2](/assets/img/2022-02-19/IoT/oneM2M application development procedure %281%29/Untitled 1.png)

- ADN-AE1: 조명 Light #1에 내장된 애플리케이션으로서 조명을 제어하고 Mca 참조 포인트를 통하여 Home Gateway MN-CSE와 상호작용하는 기능을 가지고 있다.
- ADN-AE2: 조명 Light #2에 내장된 애플리케이션으로서 조명을 제어하고 Mca 참조 포인트를 통하여 Home Gateway MN-CSE와 상호작용하는 기능을 가지고 있다.
- IN-AE: 스마트폰 디바이스에 내장된 애플리케이션으로서 Mca 참조 포인트를 통해서 oneM2M Service Platform인 IN-CSE와 직접 상호작용하여 조명 Light #1과 Light #2를 원격으로 제어하는 기능을 가지고 있다.
- MN-AE: 홈 게이트웨이 MN-CSE에 내장되어 있으며 Mca 참조 포인트를 통해서 MN-CSE와 상호작용하는 게이트웨이 애플리케이션이다.

## 기능 설계: 등록 (Registration)

---

등록(Registration)은 M2M 서비스를 사용하기 위해 AE 또는 CSE를 등록해야 한다.

| Originator (요청자) | Receiver(Registrar) (수용자) |
| --- | --- |
| ADN-AE | MN-CSE, IN-CSE |
| ASN-AE | ASN-CSE |
| MN-AE | MN-CSE |
| IN-AE | IN-CSE |
| ASN-CSE | MN-CSE, IN-CSE |
| MN-CSE | MN-CSE, IN-CSE |

## 기능 설계: 구독 (SUB - Subscription & Notification)

---

구독(Subscription)은 애플리케이션간(AE) **데이터 교환**에 유용하게 쓰이는 기능이다.

- CSE 내부에는 많은 데이터/서비스가 리소스의 형태로 저장되어 있고, 애플리케이션은 이러한 리소스를 통하여 데이터들을 읽고 쓰는 등의 사용을 할 수 있다.
- 애플리케이션이 특정 리소스에 관심이 있어 해당 리소스의 상태가 변경될 경우 자동으로 이러한 정보를 알림 받고자 할 경우 CSE가 제공하는 구독 기능을 이용할 수 있다.

| 속성 | 설명 |
| --- | --- |
| eventNotificationCriteria | Subscribed-to 리소스에 발생하는 Event 중 특정 조건을 만족하는<br/>Notification을 수신하고자 할 때 설정 |
| expirationCounter | 해당 Counter 만큼의 Notification 전송시 Subscription 리소스 삭제됨 |
| notificationURI | Notification이 전송되는 URI |
| batchNotify | 특정 시간/개수의 Notification을 통합하여 Batch Notification으로<br/>수신하고자 할 때 설정 |
| rateLimit | 특정 시간 동안 특정 개수 이상의 Notification 수신을 제한하기<br/>위해 설정 |
| notificationEventCat | Notification에 설정되는 QoS 카테고리 정보 |
| pendingNotification | Reachability 및 Schedule에 의해 전송되지 못한 Notification을 재전송<br/>하기 위한 Policy 정보 |
| notificationStoragePriority | Notification이 바로 전송되지 않고 저장될 때 저장 우선 순위 정보 |
| notificationContentType | Notification 메시지에 포함되는 데이터 구성<br/>(E.g., Modificated Attribute Only, Whole Resource) |

<br/><br/><br/>
***References***

---

- [*2강 oneM2M 애플리케이션 개발 절차(1)*](https://www.youtube.com/watch?v=8thf3Kkr3d4&list=PLOrVNg1YSZEbOMug_Ott-FmIqrTEIwDk5&index=3&ab_channel=%ED%95%9C%EA%B5%AD%EC%A7%80%EB%8A%A5%ED%98%95%EC%82%AC%EB%AC%BC%EC%9D%B8%ED%84%B0%EB%84%B7%ED%98%91%ED%9A%8C)