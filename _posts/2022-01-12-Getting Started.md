---
title: '[Git] 시작하기'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 17:00:00 +0900
categories: [Git, Getting Started]
tags: [Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

![Pro Git 2nd](/assets/img/2022-01-12/Git/Getting Started/Git Pro 2nd.png){:width="20%"}
_Pro Git 2nd_

본 Git 게시글은 도서 "**Pro Git 2nd** *스캇 샤콘, 벤 스트라웁 공저 / 박창우, 이성환, 최용재 공역*"을 참고하여 작성했습니다. 내용은 거의 대부분 도서와 동일하나 Git 2.23.0 이전 버전의 명령어를 Git에서 새로 제안하는 명령어로 바꿨고, 중간중간 추가 내용이 있으면 좋겠다 싶은 곳에는 짧게 제 의견을 추가했습니다.

※ 이미지 내 글씨가 잘 보이지 않을 경우 좌측 하단 반달 모양의 버튼을 눌러 테마를 밝은색으로 바꿔주세요.

## Git이란?

---

Git은 버전 관리 시스템(VCS, Version Control System)의 한 종류이며, 기존 중앙집중식 버전 관리 시스템(CVCS, Central Version Control System) 방식을 채택한 프로그램들 보다 조금 더 발전한 분산 버전 관리 시스템(DVCS, Distributed Version Control System)을 채택했으며, BitKeeper의 단점을 보완해 출시했다.

## Git의 장점

---

- 빠른 속도
- 단순한 구조
- 비선형적인 개발(수천 개의 동시다발적인 브랜치)
- 완벽한 분산
- 리눅스 커널 같은 대형 프로젝트에서도 유용할 것(속도나 데이터 크기 면에서)

## 파일의 세 가지 상태

---

Git은 파일을 **Committed, Modified, Staged** 세 가지 상태로 관리한다.

- **Committed**: 데이터가 로컬 데이터베이스에 저장된 상태
- **Modified**: 수정한 파일을 아직 로컬 데이터베이스에 커밋하지 않은 상태
- **Staged**: 수정한 파일을 곧 커밋할 것이라고 표시한 상태

위 세 가지 상태는 Git 프로젝트의 세 단계와 연결된다.

![The three states](/assets/img/2022-01-12/Git/Getting Started/The three states.png)
_The three states_

- **Working Directory**: 프로젝트의 특정 버전을 Checkout 한 것
.git Directory 안에 압축된 데이터베이스에서 파일을 가져와서 Working Directory를 생성함
- **Staging Area**: .git Directory 내부에 존재하며 단순한 파일이고, 곧 커밋할 파일에 대한 정보를 저장함
종종 “index”라고 불리기도 하지만, Staging Area라는 명칭이 표준이 되어가고 있음
- **.git Directory**: 프로젝트의 메타데이터와 객체 데이터베이스를 저장하는 곳
