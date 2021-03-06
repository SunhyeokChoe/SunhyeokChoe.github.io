---
title: "Monolith, Multi-Repo, Mono-Repo"
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-02-15 21:10:21 +0900
categories: [Terminology, Project Management]
tags: [Monolith, Multi-Repo, Mono-Repo, Yarn Workspace, Lerna]
math: true
mermaid: true
pin: false
---

![Monolith, Multi-Repo, Mono-Repo](/assets/img/2022-02-15/Terminology/Monolith, Multi-Repo, Mono-Repo/Monolith, Multi-Repo, Mono-Repo.png)
_Monolith, Multi-Repo, Mono-Repo_

프로젝트를 구성하는 방식에는 `모놀리스`, `멀티레포`, `모노레포` 세 가지 방식이 있다.

`모놀리스` 구조는 하나의 레포지토리 안에 하나의 패키지 안에 여러 개의 서비스가 폴더로 구분된다. `멀티레포` 구조는 하나의 레포지토리 안에 하나의 패키지 안에 하나의 서비스가 들어간다. `모노레포` 구조는 하나의 레포지토리 안에 큰 공통 패키지 하나 안에 여러 개의 서브 패키지들(서비스)이 들어있다.

`모놀리스` 구조는 비슷한 여러 개의 소규모 프로젝트들을 한 번에 관리할 때 좋은 구조이다.

`멀티레포` 구조는 서비스의 규모가 커서 각 서비스를 별도 관리해서 서비스 간 의존성을 줄이고 싶을 때 쓰면 좋은 구조인 것 같다. 마이크로 서비스 아키텍처라고도 불린다.

`모노레포` 구조는 마찬가지로 서비스의 규모가 커졌을 때, 각 서비스 간 분리도 되면서 공통 구조를 가져가고 싶을 때 사용하면 좋다.

## **모노레포**

---

모노레포라는것은 1개의 `git repository`가 존재한다는 뜻이다. 그 레포지토리안에는 하나의 큰 공통 패키지 안에 여러 개의 서브 패키지가 들어있다.

## **모노레포의 장점**

---

**1. 패키지별로 배포하기 쉬워진다.**

모노레포의 서브 패키지 `A`와 `B`가 있다고 해보자. A 패키지는 B 패키지의 모듈을 가져와서 사용한다. 이때 A 패키지에 새로운 기능을 추가하고 나서 CI 절차를 밟을 시 B 패키지에 문제를 일으키지 않고 A 패키지가 배포된다.

**2. 의존성 관리가 간편해진다.**

하나의 공통 패키지 안에 여러 서브 패키지들이 있기 때문에 각 서브 패키지들이 공통으로 사용하는 의존성 들은 공통 패키지에 넣으면 된다. 각 서브 패키지별로 다르게 사용해야 하는 의존성 들은 각 서브 패키지에 명시하면 된다.

멀티레포였으면 이렇게 여러 개의 레포지토리에서 공통으로 사용하는 의존성 관리를 하기가 어려워진다.

**3. 이슈관리가 간편해진다.**

멀티레포였으면 각 레포지토리 별로 이슈가 분산된다. A 와 B 레포지토리 둘 다에서 발생한 문제인데 어떤 경우에는 A 레포지토리에만 이슈를 올리고 B 에는 올리지 않을 수도 있다. 사람이 모든 레포지토리를 매번 관리하기가 힘들기 때문이다. 하지만 모노레포는 하나의 레포지토리에 모든 코드가 몰려있으므로 그 레포지토리에서 이슈를 생성하고 히스토리가 쌓이므로 이슈 트래킹에 이점이 생기게 되는 것이다.

**4. 각 서브 패키지가 독립적이면서, 서로를 공유할 수 있다.**

**개인적으로 모노레포의 가장 큰 장점이라고 생각하는 부분이다.** 각 서브 패키지들은 서로를 `심볼릭 링크`로 연결한다. 이 `심볼릭 링크` 연결이 무슨 뜻이냐면, A 서브 패키지의 수정사항이 B 서브 패키지에 바로바로 반영된다는 것이다. 각 패키지를 수정한 다음에 배포하지 않고서도 로컬 컴퓨터에서 의존하고 있는 다른 패키지에 바로바로 반영되기 때문에 개발 생산성이 매우 올라가게 된다.

이걸 직접 구현할 수도 있지만 `Yarn Workspace`나 `Lerna`를 사용하면 좀 더 간편하게 사용할 수 있다.

## **멀티레포 vs 모노레포**

---

멀티레포는 말 그대로 레포지토리가 여러 개 있다는 뜻이다. 레포지토리가 여러 개면 좋은 점이 있다. **바로 권한 관리를 레포지토리 별로 따로 할 수 있다는 것이다.** 예를 들어, 모든 레포지토리를 총괄하는 개발자, 특정 레포지토리의 모더레이터로 할당된 개발자들, 그 밑에 협력자로 할당된 실제 개발을 진행하는 개발자들 등 각 레포지토리 별로 개발자들에 권한을 부여해 체계적인 서비스 관리가 가능하다.

모노레포는 레포지토리 하나에 공통 패키지가 하나 있고, 그 하위에 여러 서브 패키지들이 존재한다. 이러한 구조가 어느 상황에 유용할까? 예컨대 스타트업에서는 개발자 입/퇴사가 상당히 빈번한데, 이런 상황에서 프로젝트가 멀티레포의 방식으로 구성되어 있다면 레포지토리 별로 개발자에게 권한을 부여하고 제거하는 작업 또한 매우 빈번할 것이다. 이럴 때 모노레포 방식으로 프로젝트를 만들어두면 협업자 관리하는 데에 비용이 거의 들지 않게 된다. 또한 하나의 레포지토리에서 히스토리 선형으로 쌓여 있으므로 이슈 트래킹에도 큰 이점으로 작용할 수 있다. 그리고 개발자 한 명이 휴가를 가거나 부재 상태일 경우, 평소 서로의 서브 패키지를 크로스로 봐주고 있던 상황이라면 그 공백을 다른 개발자가 메꾸어 주기가 멀티레포에 비해 쉽다. 멀티레포는 본인의 영역만 담당하는 경우가 일반적이기 때문이다. **이런 점은 모노레포의 장점이자 단점이라고 할 수 있다.**

정리하면, 모노레포가 멀티레포보다 좋은 점은 **모노레포는 공통화가 쉽다는 것이다.** 서비스가 한두 개일 땐 상관없는데 이게 50개 100개가 되는 경우 멀티레포로 이 모든 서비스를 관리하게되면 굉장히 힘들어진다.

따라서, 모노레포로 프로젝트를 구성해두면 하나의 큰 패키지 안에 여러 개의 서브 패키지들로 서비스들을 관리할 수 있기 때문에 멀티레포보다 훨씬 관리하기가 편하다.

어느 것이 무조건 좋다는 것은 없다. 서로 장단점이 있으므로 프로젝트 성격에 맞게 적절히 골라 구성하면 된다.

## **모노레포 구성**

---

- **Lerna**

모노레포 구성을 쉽게 할 수 있도록 도와주는 라이브러리이다. 모노레포 안의 각 패키지를 묶어서 버전관리할지, 패키지마다 버전관리 할지 등을 정할 수 있다.

- **Yarn Workspace**

NPM과 비슷한 패키지 매니저인 Yarn에서 Workspace 기능을 제공한다. 이 기능을 사용하면 모노레포의 각 서브 패키지 안에 있는 `node_modules`가 프로젝트 루트 패키지의 `node_modules`를 참조하게 된다.

Yarn에서 WorkSpace란 패키지와 동의어로 사용된다. 각 서브 패키지들이 독립적이므로 어떤 프로젝트에는 타입스크립트 빌드 설정파일인 tsconfig.json을 넣을수도 있고 다른 서브 패키지에서는 그냥 jsconfig.json으로 구성할 수도 있다.

Yarn Workspace에서는 각 서브 패키지들의 의존성이 모두 루트에서 관리된다. 관리 자체는 루트에서 되지만 각 서브 패키지별로 의존성 재설치는 할 수 있다. 하지만 Lerna는 패키지별로도 관리하고 루트에서도 관리한다. 이런 점이 두 라이브러리의 차이점이라고 할 수 있다.

## **Lerna로 모노레포 구성해보기**

---

`Lerna`를 사용해서 모노레포를 구성해보면 다음과 같은 프로젝트 구조가 생긴다.

```
mylerna_repo/-   node_modules
-   packages    -   sub_package1
        package.json    -   sub_package2
        package.json    -   sub_package3
        package.jsonlerna.json
package.json // 루트 패키지
```

`mylerna_repo`라고 하는 하나의 큰 레포지토리 안에 루트 패키지 하나가 존재한다. 그 루트 패키지 안에는 `packages`폴더로 구분된 여러 개의 서브 패키지들이 들어있다. 각 서브 패키지에 들어있는 `package.json`으로 독립적인 의존성 관리가 가능해진다. 공통으로 관리되는 의존성은 루트의 `package.json`으로 관리된다.

온라인 IDE로 유명한 **[코드 샌드박스](https://github.com/codesandbox/codesandbox-client)**는 `Yarn Workspace`와 `Lerna`를 혼합해서 사용하고 있다.

`Lerna`는 다음과 같은 두 가지 방법으로 사용할 수 있다.

1. NPM 저장소에 push 없이 사용하기
2. NPM 저장소에 push 하면서 사용하기

첫 번째 방식을 사용하게 되면 각 서브 패키지들을 `심볼릭 링크`로 연결해서 사용한다. 이걸 `Lerna`가 자동으로 처리해주기 때문에 내가 신경 쓰지 않아도 된다.

두 번째 방식을 사용하게 되면 **import { something } from @name/package_name;**와 같이 NPM 저장소에 올라가있는 패키지를 받아오기 때문에 패키지를 매번 수정하고 push하고 다시 받아와서 테스트를 해봐야 해서 번거롭다.

### **lerna bootstrap 명령어 살펴보기**

---

```
A
  package.json
  node_modules/
    BB
  package.json
```

**아래와 같은 명령어는 왜 사용해야 하는가?**

이런 프로젝트 구조가 있다고 해보자. A 서브 패키지에서 B 서브 패키지를 사용하고 있다. 이때 A 패키지에서 node_modules 안에 있는 B 패키지를 사용하면 B 패키지의 내용이 수정됬을 때 그 수정사항이 반영되지 않은 채로 실행된다. 따라서 의존관계에 있는 A 와 B 패키지를 로컬 컴퓨터에서 서로 `심볼릭 링크`로 연결해야 한다.

만약 `mylerna_repo`에 100개의 서브 패키지가 있다고 해보자. 이거 일일이 `심볼릭 링크`로 연결할 자신이 있는가? 어마어마하게 귀찮은 작업이 될 것이다. 이 과정에서 실수할 수도 있다.

이러한 문제 발생을 방지하기 위해 `lerna bootstrap`명령어를 실행해 의존관계에 있는 서브 패키지들이 서로 `심볼릭 링크`로 자동 연결되도록 한다.

뿐만 아니라 이 명령어는 각 서브패키지들의 `package.json`의 외부모듈 설치도 해준다. 이 `package.json` 안에 다른 서브 패키지가 참조되어 있으면 새로 설치하지 않고 `심볼릭 링크`로 연결한다.
