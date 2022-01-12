---
title: '[Git] 파일 이름 변경하기'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 18:02:00 +0900
categories: [Git, Getting Started]
tags: [git mv, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

Git은 다른 VCS와는 달리 파일 이름의 변경이나 파일의 이동을 명시적으로 관리하지 않는다. 다시 말해서 파일 이름이 변경됐다는 별도의 정보를 저장하지 않는다. Git은 굳이 파일 이름이 변경되었다는 것을 추적하지 않아도 아는 방법이 있다.

이렇게 말하고 Git에 mv 명령이 있는 게 좀 이상하겠지만, 아래와 같이 파일 이름을 변경할 수 있다.

```bash
$ git mv file_from file_to
```

잘 동작한다. 이 명령을 실행하고 Git의 상태를 확인해보면 Git은 이름이 바뀐 사실을 알고 있다.

```bash
$ git mv README.md README
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed: README.md -> README
```

사실 git mv 명령은 아래 명령어를 수행한 것과 완전 똑같다.

```bash
$ mv README.md README
$ git rm README.md
$ git add README
```

`git mv` 명령은 일종의 단축 명령어이다. 이 명령으로 파일 이름을 바꿔도 되고 `mv` 명령으로 파일 이름을 직접 바꿔도 된다. 단지 Git의 `mv` 명령은 편리하게 명령을 세번 실행해주는 것뿐이다. 어떤 도구로 이름을 바꿔도 상관없다. 중요한 것은 이름을 변경하고 나서 꼭 `rm/add` 명령을 실행해야 한다는 것이다.