---
title: "1. oneM2M 기술규격 및 기술문서 소개"
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-02-19 00:05:08 +0900
categories: [IoT, oneM2M]
tags: [IoT, oneM2M]
math: true
mermaid: true
pin: false
---

## 개요

---

oneM2M은 에너지, 교통, 국방, 공공서비스 등 산업별로 종속적이고 폐쇄적으로 운영되는, 파편화된 서비스 플랫폼 개발 구조를 벗어나 응용서비스 인프라(플랫폼) 환경을 통합하고 공유하기 위한 사물인터넷 공통서비스 플랫폼 개발을 위해 발족된 사실상 표준화 단체이다.

전세계 지역별 표준 개발 기구인 TTA(한국), ETSI(유럽), ATIS/TIA(북미), CCSA(중국), ARIB/TTC(일본) 등 7개의 SDO(Standard Development Organization)가 공동으로 설립했다.

![oneM2M 표준이 적용된 인프라 형태](/assets/img/2022-02-19/IoT/oneM2M technical standards and technical documents/oneM2M 표준이 적용된 인프라 형태.png)
_oneM2M 표준이 적용된 인프라 형태_

| Domain | Node | Description |
| --- | --- | --- |
| Infrastructure<br/>Domain | Infrastructure<br/>Node (IN) | 인프라 도메인에 위치한 IN-CSE를 포함하는 서버 기기를 의미함. <br/> IN은 서비스 제공자 당 한 개의 IN을 지원하는 것으로 정의되며,<br/>IN은 한 개의 CSE로만 구성이 되거나 1개 이상의 AE를 포함하는<br/>형태로 구성될 수 있다. |
| Field Domain | Middle Node (MN) | 필드 도메인에 위치한 MN-CSE를 포함하는 논리적 기기로,<br/>일반적으로 여러 센서나 엑추에이터들이 연결되는 게이트웨이가<br/>이에 해당한다. MN은 한 개의 CSE로 구성되거나,<br/>하나의 CSE에 1개 이상의 AE를 포함하는 형태로 구성될 수 있다. |
| | Application Service<br/>Node (ASN) | 필드 도메인에 위치한 ASN-CSE와 ASN-AE를 포함하고 있는<br/>논리적 기기이다. ASN은 한 개의 CSE와 1개 이상의 AE를 포함하는<br/>형태로 구성된다. M2M 디바이스라고 생각하면 된다. |
| | Application Dedicated<br/>Node (ADN) | 필드 도메인에 위치한 ADN-AE를 포함하고 CSE를 포함하지 않는<br/>논리적 기기이다. ADN은 CSE가 1개 이상의 AE를 포함한다.<br/>센서나 엑츄에이터같이 리소스가 부족한 디바이스라고 생각하면<br/>된다. |

| Entity | Description |
| --- | --- |
| Application Entity (AE) | M2M 서비스를 제공하기 위한 애플리케이션 기능을 포함하는 논리적인<br/>엔티티를 의미한다. 각각의 AE는 유일한 AE 식별자인 AE-ID로 구별된다.<br/>애플리케이션이라고 생각하면 된다. |
| Common Service Entity (CSE) | oneM2M 서비스 플랫폼에서 공통적으로 제공되어야 하는 서비스 기능을<br/>제공하는 부분으로서, 컴퓨터 시스템에서 미들웨어 소프트웨어에 해당한다.<br/>oneM2M에서 정의한 CSE에는 총 12개의 Common Service Function (CSF)<br/>공통 서비스 기능을 포함하고 있다. 미들웨어라고 생각하면 된다. |
| Network Service Entity (NSE) | CSE가 위치한 미들웨어 하부 네트워크 서비스(underlying network service)에<br/>대한 추상화 영역으로 CSE에게 네트워크 서비스를 제공한다. |

## 공통 서비스 기능 (Common Service Function, CSF)

---

공통 서비스 엔티티에 포함되는 다양한 공통 서비스 기능들은 **12가지로 구성**되어 있으며, 이러한 기능들은 자원(Resources) 기반으로 **Mcc**, **Mca**, **Mcn** 참조 포인트들을 통해 제공된다.

![Common Service Entity (CSE)](/assets/img/2022-02-19/IoT/oneM2M technical standards and technical documents/Common Service Entity (CSE).png)
_Common Service Entity (CSE)_

| CSF | Description |
| --- | --- |
| Registration | CSE 혹은 AE를 등록하는 기능 |
| Discovery | 리소스에 포함되어 있는 정보를 검색하는 기능 |
| Security | 보안 연결 제공 및 인증/권한 설정 기능 |
| Group Management (GMG) | 그룹 멤버 리소스를 관리하는 기능 |
| Data Management & Repository (DMR) | 데이터 저장 및 관리 기능 |
| Subscription & Notification (Sub/noti) | 리소스 변경에 대한 구독/알림 기능 |
| Device Management (DMG) | 디바이스 관리 기능 |
| Network Service Exposure/Service Ex+Triggering (NSSE) | 네트워크 연동 기능 |
| Location | 위치정보 제공 기능 |
| Communication Management/Delivery Handling (CMDH) | 데이터 전달 서비스를 제공하는 기능 |
| Service Charging & Accounting (SCA) | oneM2M 서비스 플랫폼을 통해서 제공되는<br/>서비스의 과금 체계 및 방법에 대한 기능 |
| Application and Service Layer Management (ASM) | IN, MN, ASN, AND에 위치한 AE, CSE 소프트웨어를<br/>관리하는 기능 |

| Reference Point | Description |
| --- | --- |
| M2M Communication<br/>with AE (Mca) | Mca는 AE와 CSE 간의 API 통신을 위한 연결 포인트이다. |
| M2M Communication<br/>with CSE (Mcc) | Mcc는 CSE와 다른 CSE간의 통신 연결 포인트이다. |
| M2M Communication<br/>with NSE (Mcn) | Mcn은 CSE와 NSE의 통신 연결 포인트이며, NSE에서 제공되는<br/>네트워크 서비스 기능을 이용할 수 있는 연결 포인트이다. |
| M2M Communication<br/>with CSE of Different M2M Service Provider | Mcc'는 서로 다른 서비스 제공자에 종속적인 CSE간의 포인트를<br/>가리키며, 서비스 제공자 간 CSE 사이의 통신을 지원하는<br/>연결 포인트이다. |

## 간략한 아키텍처 참조 모델 3 가지 레이어

---

![간략한 아키텍처 참조 모델 3 가지 레이어](/assets/img/2022-02-19/IoT/oneM2M technical standards and technical documents/간략한 아키텍처 참조 모델 3 가지 레이어.png)
_간략한 아키텍처 참조 모델 3 가지 레이어_

## 아키텍처 참조 모델

---

![아키텍처 참조 모델](/assets/img/2022-02-19/IoT/oneM2M technical standards and technical documents/아키텍처 참조 모델.png)
_아키텍처 참조 모델_

**특징**

- NoDN과 연결되는 노드는 반드시 하나 이상의 AE를 갖는다.
- IN, MN 등 데이터 흐름만을 제어하는 노드는 AE를 갖거나 갖지 않을 수 있다.
- ADN의 경우 AE가 게이트웨이(MN)를 거치지 않고 바로 IN의 CSE와 Mca로 연결될 수 있다.
- ASN의 경우 CSE가 게이트웨이를 거치지 않고 바로 IN의 CSE와 Mcc로 연결될 수 있다.
- CSE를 가지는 MN간 Mcc로 연결될 수 있다. (MN-CSE ↔ IN-CSE or MN-CSE ↔ MN-CSE)
- ADN-AE → MN-CSE → IN-CSE 연결 방식이 가장 일반적인 형태라고 볼 수 있다.
- oneM2M 규격을 따르지 않는 장치들(NoDN)도 실제 규격에는 명시되어 있지 않지만, proxy를 통해 IN, MN, ASN에 연결할 수 있다.
Interworking Proxy Entity(IPE)를 규격에 넣고 정의하고 있지만, IoT 관련 규격이 전세계적으로 다양하므로 빠른 대응 및 Realse는 어려워 현재 연동 테스트 단계에 있다.

<br/><br/><br/>
***References***

---

- [*1강 oneM2M 기술규격 및 기술문서 소개 (Youtube)*](https://www.youtube.com/watch?v=DZYB3awJ_i4&ab_channel=%ED%95%9C%EA%B5%AD%EC%A7%80%EB%8A%A5%ED%98%95%EC%82%AC%EB%AC%BC%EC%9D%B8%ED%84%B0%EB%84%B7%ED%98%91%ED%9A%8C)