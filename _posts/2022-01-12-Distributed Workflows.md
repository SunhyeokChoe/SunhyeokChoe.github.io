---
title: '[Git] Centralized, Integration-Manager and Benevolent dictator workflow'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 20:43:00 +0900
categories: [Git, Workflow]
tags: [Workflow, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

이번 글에서는 프로젝트 기여자 또는 수정사항을 취합하는 관리자의 관점에서 작업물을 프로젝트에 어떻게 포함시킬지와 수많은 개발자가 수행한 일을 취합하고 프로젝트를 운영하는 방법을 배운다.

## 분산 환경에서의 워크플로

---

중앙집중형 버전 관리 시스템과는 달리 Git은 분산형이다. Git은 구조가 매우 유연하기 때문에 여러 개발자가 협업하는 과정을 더 다양하게 구성할 수 있다. 중앙집중형 버전 관리 시스템에서 각 개발자는 중앙 저장소를 중심으로 뻗어 나온 하나의 노드일 뿐이다. 하지만 Git에서는 각 개발자의 저장소가 하나의 노드이기도 하고 중앙 저장소 같은 역할도 할 수 있다. 즉, 모든 개발자는 다른 개발자의 저장소에 작업한 내용을 전송하거나, 다른 개발자들이 본인의 프로젝트에 참여할 수 있도록 본인이 운영하는 저장소의 위치 공개 및 권한을 부여할 수도 있다. 이러한 특징은 팀이 프로젝트를 운영할 때 다양한 워크플로를 적용시킬 수 있도록 해준다. 이런 유연성을 살려 저장소를 운영하는 몇 가지 방식을 소개한다. 각 방식의 장단점을 살펴보고 그 방식 중 하나를 고르거나 여러 가지를 적절히 섞어 사용하면 된다.

## Centralized Workflow

---

중앙집중형 시스템에서는 보통 중앙집중형 협업 모델이라는 한 가지 방식의 워크플로밖에 없다. 중앙 저장소는 딱 하나 있고 변경 사항은 모두 이 중앙 저장소에 저장된다. 개발자는 이 중앙 저장소를 중심으로 작업하게 된다.

![Centralized Workflow](/assets/img/2022-01-12/Git/Distributed Workflows/Centralized Workflow.png)
_Centralized Workflow_

중앙집중형 워크플로에서 개발자 세 명이 저장소를 Clone 하고 각자 작업을 진행하는 상황을 생각해보자. 한 개발자가 자신이 한 일을 커밋하고 나서 아무 문제 없이 서버에 Push 한다. 그러면 다른 개발자는 자신의 일을 커밋하고 Push 하기 전에 첫 번째 개발자가 한 일을 먼저 Merge 해야 한다. Merge를 해야 첫 번째 개발자가 작업한 내용을 덮어쓰지 않는다. 이런 개념은 Subversion과 같은 중앙집중형 버전 관리 시스템에서 사용하는 방식이고 Git에서도 당연히 이런 워크플로를 사용할 수 있다.

팀의 규모가 작거나 이미 중앙집중형에 적응한 상황이라면 이 워크플로에 따라 Git을 도입하여 사용할 수 있다. 중앙 저장소를 하나 만들고 개발자 모두에게 Push 권한을 부여한다. 모두에게 Push 권한을 부여해도 Git은 한 개발자가 다른 개발자의 작업 내용을 덮어쓰도록 허용하지 않는다. 철수랑 영희가 동시에 같은 부분을 수정하는 상황을 생각해보자. 철수가 먼저 작업을 끝내고 수정한 내용을 서버로 Push 했다. 영희도 마찬가지로 작업을 마치고 수정한 내용을 서버로 Push 하려 하지만 서버가 받아주지 않는다. 서버에는 이미 철수가 수정한 내용이 추가되었기 때문에 Push 하기 전에 Fetch로 받아서 Merge 한 후 Push 할 수 있다. 이런 개념은 중앙집중형 워크플로에 익숙한 개발자들도 거부감 없이 도입할 수 있다.

작은 팀만 이렇게 일할 수 있는건 아니다. Git이 제공하는 브랜치 관리 모델을 사용하면 수백명의 개발자가 한 프로젝트 안에서 다양한 브랜치를 만들어서 함께 작업하는 것도 쉽다.

## Integration-Manager Workflow

---

Git을 사용하면 리모트 저장소를 여러 개 운영할 수 있다. 다른 개발자는 읽기만 가능하고 자신은 읽기 쓰기 모두 가능한 공개 저장소를 만드는 방식도 가능하다. 이 방식의 워크플로에는 보통 프로젝트를 대표하는 공식 저장소가 있다. 기여자는 우선 공식 저장소를 Clone 하고 수정하고 나서 자신의 저장소에 Push 한다. 그 다음에 프로젝트 Integration-Manager(통합을 수행하는 관리자)에게 기여자의 저장소에서 Pull 하라고 요청한다. 그러면 Integration-Manager는 기여자의 저장소를 리모트 저장소로 등록하고, 로컬에서 기여물을 테스트하고, 프로젝트 메인 브랜치에 Merge 하고, 그 내용을 다시 프로젝트 메인 저장소에 Push 한다. 이런 과정은 다음과 같다.

![Integration-Manager Workflow](/assets/img/2022-01-12/Git/Distributed Workflows/Integration-Manager Workflow.png)
_Integration-Manager Workflow_

1. 기여자는 메인 저장소를 Clone 하고 수정한다.
2. 기여자는 자신의 저장소에 Push 하고 Integration-Manager가 접근할 수 있도록 공개해 놓는다.
3. 기여자는 Integration-Manager에게 변경사항을 적용해 줄 것을 요청한다.
4. Integration-Manager는 기여자의 저장소를 리모트 저장소로 등록하고 수정사항을 Merge 하여 테스트한다.
5. Integration-Manager는 Merge 한 사항을 메인 저장소에 Push 한다.

이 방식의 장점은 기여자와 Integration-Manager가 각자의 상황에 맞춰 프로젝트를 진행할 수 있다는 점이다. 기여자는 자신의 저장소와 브랜치에서 수정 작업을 계속해 나갈 수 있고 수정사항이 반영될 때까지 기다릴 필요가 없다. 그리고 Integration-Manager는 여유를 가지고 필요에 따라 기여자가 Push 해 놓은 커밋을 적절한 시점에 Merge 한다.

## Dictator and Lieutenants Workflow

---

이 방식은 여러개의 저장소를 운영하는 방식을 변형한 구조를 갖는다. 보통 수백 명의 개발자가 참여하는 아주 큰 프로젝트를 운영할 때 이 방식을 사용한다. Linux 커널 프로젝트가 대표적이다. 여러 명의 Integration-Manager가 저장소에서 자신이 맡은 부분만을 담당하는데 이들을 ***Lieutenants*** 라고 부른다. 모든 Lieutenant는 최종 관리자 아래에 있으며 이 최종 관리자를 ***Benevolent Dictator*** 라고 부른다. Benevolent Dictator는 Lieutenant의 저장소를 가져와 공식 저장소에 Push 하고 모든 프로젝트 참여자는 이 공식 저장소에서 반드시 Pull 해야 한다. 이러한 워크플로는 다음과 같다.

![Benevolent dictator Workflow](/assets/img/2022-01-12/Git/Distributed Workflows/Benevolent dictator Workflow.png)
_Benevolent dictator Workflow_

1. 개발자는 코드를 수정하고 master 브랜치를 기준으로 자신의 토픽 브랜치를 Rebase 한다. 여기서 master 브랜치란 공식 저장소의 브랜치를 말한다.
2. Lieutenant들은 개발자들의 수정사항을 자신이 관리하는 master 브랜치에 Merge 한다.
3. Dictator는 Lieutenant의 master 브랜치를 자신의 master 브랜치로 Merge 한다.
4. Dictator는 자신의 master 브랜치를 Push 하며 다른 모든 개발자는 Dictator의 master 브랜치를 기준으로 Rebase 한다.

이 방식이 일반적이지는 않지만 깊은 계층 구조를 가지는 환경이나 규모가 큰 프로젝트에서는 매우 쓸모 있다. 프로젝트 리더가 모든 코드를 통합하기 전에 코드를 부분부분 통합하도록 여러 명의 Lieutenant에게 위임한다.

## 워크플로 요약

---

이 세 가지 워크플로가 Git 같은 분산 버전 관리 시스템에서 주로 사용하는 것들이다. 사실 이런 워크플로뿐만 아니라 다양한 변종 워크플로가 실제로 사용된다. 어떤 방식을 선택하고 혹은 조합해야 하는 지 살짝 감이 잡힐 것이다. 앞으로 몇 가지 구체적 사례를 들고 우리가 다양한 환경에서 각 역할을 어떻게 수행하는지 살펴본다. 이어지는 내용에서 프로젝트에 참여하고 기여할 때 작업 패턴이 어떠한지 몇 가지 살펴보기로 한다.