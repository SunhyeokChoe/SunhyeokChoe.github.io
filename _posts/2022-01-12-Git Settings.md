---
title: '[Git] 최초 설정'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 17:11:00 +0900
categories: [Git, Getting Started]
tags: [Git Settings, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

git config라는 도구로 설정 내용을 확인하고 변경할 수 있다. Git은 이 설정에 따라 동작한다.

※ **파일(fedora 혹은 ubuntu(debian 류) 등 리눅스 환경에서의 경로)**

1. **linux: /etc/gitconfig**
Windows (`$HOME` 디렉토리에서 .gitconfig 파일 검색)  
C:\Users\$USER\.gitconfig 시스템의 모든 사용자와 모든 저장소에 적용되는 설정이다.  
`git config --system` 옵션으로 이 파일을 읽고 쓸 수 있다.
2. **linux: ~/.gitconfig, ~/.config/git/config**  
특정 사용자에게만 적용되는 설정이다.  
`git config --global` 옵션으로 이 파일을 읽고 쓸 수 있다.
3. **.git/config**  
Git 디렉터리($프로젝트/.git/)에 있고 특정 저장소(혹은 현재 작업 중인 프로젝트)에만 적용된다.

각 설정은 역순으로 우선시 된다.

**.git/config > ~/.gitconfig, ~/.config/git/config > /etc/gitconifg**

## 사용자 정보

---

Git을 설치하고 나서 가장 먼저 해야 할 일은 사용자 이름과 이메일 주소를 설정하는 것이다. Git은 커밋할 때마다 이 정보를 사용한다. **한번 커밋한 후에는 정보를 변경할 수 없다.**

```bash
$ git config --global user.name "SunhyeokChoe"

$ git config --global user.email "hackerwreckers@gmail.com"
```

--global 옵션으로 설정하는 것은 **딱 한 번만 하면 된다.** 해당 시스템에서 해당 사용자가 사용할 때에는 이 정보를 사용한다. 만약 프로젝트마다 다른 이름과 이메일 주소를 사용하고 싶으면 --global 옵션을 빼고 명령을 실행한다.

## 설정 확인

---

`git config --list` 명령을 실행하면 설정한 모든 값을 보여준다.

Git은 같은 키를 여러 파일(/etc/gitconfig와 ~/.gitconfig 같은)에서 읽기 때문에 같은 키가 여러 개 있을 수도 있다. 그러면 Git은 나중 값을 사용한다.

`$ git config <key>` 명령으로 Git의 특정 key에 대해 어떤 값을 사용하는지 확인할 수 있다.

ex) `$ git config user.name`