---
title: '[Git] 저장소 만들기'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 17:20:00 +0900
categories: [Git, Getting Started]
tags: [Repository, Git Clone, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

Git 저장소를 만드는 방법은 두 가지다.

- 기존 프로젝트를 Git 저장소로 만들기
- 다른 서버에 있는 저장소를 Clone하기

## 기존 프로젝트를 Git 저장소로 만들기

---

해당 프로젝트의 디렉터리로 이동 후 아래 명령 실행.

`$ git init`

이 명령은 .git이라는 하위 디렉터리를 만든다. .git 디렉터리에는 저장소에 필요한 뼈대 파일(skeleton)이 들어 있다. 이 명령으로만으로는 아직 프로젝트의 어떤 파일도 관리하지 않는다. Git이 파일을 관리하게 하려면 저장소에 파일을 추가하고 커밋해야 한다.

git add 명령으로 파일을 추가하고 git commit 명령으로 커밋한다.

```bash
$ git add *.c
$ git add LICENSE
$ git commit -m "initial project version"
```

## 다른 서버에 있는 저장소를 Clone하기

---

다른 프로젝트에 참여하거나(contribute) Git 저장소를 복사하고 싶을 때 git clone 명령을 사용한다. git clone을 실행하면 프로젝트 히스토리를 전부 받아온다. 서버의 디스크가 망가져도 클라이언트 저장소 중에서 아무거나 하나 가져다가 복구하면 문제 없다. 서버에만 적용했던 설정은 복구하지 못하지만 모든 데이터는 복구된다.

```bash
$ git clone https://github.com/facebook/react.git
```

이 명령은 "react"라는 디렉터리를 만들고 그 안에 .git 디렉터리를 만든다. 그리고 저장소의 데이터를 모두 가져와서 자동으로 가장 최신 버전을 Checkout 해 놓는다. react 디렉터리로 이동하면 Checkout으로 생성한 파일을 볼 수 있고, 당장 하고자 하는 일을 시작할 수 있다.

아래와 같은 명령을 사용하여 저장소를 Clone하면 "react"가 아니라 다른 디렉터리 이름으로 Clone 할 수 있다.

```bash
$ git clone https://github.com/facebook/react.git myReact
```

Git은 다양한 프로토콜을 지원한다. 이제까지는 https:// 프로토콜을 사용했지만, git:// 를 사용할 수도 있고 user@server:path/to/repo.git 처럼 SSH 프로토콜을 사용할 수도 있다.