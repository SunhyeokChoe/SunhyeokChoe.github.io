---
title: '[Git] Alias'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 18:44:00 +0900
categories: [Git, Getting Started]
tags: [git config, Git Alias, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

Git의 명령을 전부 입력하는 것이 귀찮다면 `git config`를 사용하여 각 명령을 단축하여 사용할 수 있도록 설정하면 된다.

```bash
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

이제 git commit 대신 git ci 만으로도 커밋할 수 있다. 이 처럼 자주 사용하는 명령들을 Alias를 등록해 편하게 사용할 수 있다.

이미 있는 명령 또한 축약해 사용할 수 있다. 예를 들어 파일을 Unstaged 상태로 만드는 명령을 만들어서 불편함을 덜 수 있다.

```bash
$ git config --global alias.unstage 'restore --staged'

or

$ git config --global alias.unstage 'reset HEAD --'
```

아래 세 명령은 동일한 명령이 된다.

```bash
$ git unstage fileA
$ git restore --staged fileA
$ git reset HEAD fileA
```

한결 간결해졌다. 추가로 last 명령을 만들어 보자.

```bash
$ git config --global alias.last 'log -1 HEAD'
```

이제 최근 커밋을 좀 더 쉽게 확인할 수 있다.

![Untitled](/assets/img/2022-01-12/Git/Git Aliases/Untitled.png)

Git의 명령어 뿐만 아니라 외부 명령어도 실행할 수 있다. !를 맨 앞에 추가하면 외부 명령을 실행한다. 커스텀 스크립트를 만들어 사용할 때 매우 유용하다. 아래 명령은 git visual이라고 입력하면 extScr가 실행된다.

```bash
$ git config --global alias.visual '!extScr'
```