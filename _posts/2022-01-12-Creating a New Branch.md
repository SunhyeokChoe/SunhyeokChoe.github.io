---
title: '[Git] 브랜치 및 커밋 개체 생성 과정'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 19:00:00 +0900
categories: [Git, Branch]
tags: [git branch, git switch, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

Git이 브랜치를 다루는 과정을 이해하려면 Git이 데이터를 어떻게 저장하는지 알아야 한다.

Git은 데이터를 변경사항(Diff)으로 기록하지 않고 일련의 스냅샷으로 기록한다.

커밋하면 Git은 현 Staging Area에 있는 데이터의 스냅샷에 대한 포인터, 저자나 커밋 메시지 같은 메타데이터, 이전 커밋에 대한 포인터 등을 포함하는 커밋 개체(커밋 Object)를 저장한다. 이전 커밋 포인터가 있어서 현재 커밋이 어떤 커밋을 기준으로 바뀌었는지를 알 수 있다. 최초 커밋을 제외한 나머지 커밋은 이전 커밋 포인터가 적어도 하나씩 있고 브랜치를 합친 Merge 커밋 같은 경우에는 이전 커밋 포인터가 여러개 있다.

파일이 3개 있는 디렉터리가 하나 있고 이 파일을 Staging Area에 저장하고 커밋하는 예제를 살펴보자. 파일을 Stage하면 **Git 저장소에 파일을 저장**하고(Git은 이것을 Blob이라고 부른다) Staging Area에 **해당 파일의 체크섬을 저장**한다.

```bash
$ git add README test.rb LICENSE
$ git commit -m 'initial commit'
```

`git commit`으로 커밋하면 먼저 루트 디렉터리와 각 하위 디렉터리의 트리 개체를 체크섬과 함께 저장소에 저장한다. 그 다음에 커밋 개체를 만들고 메타데이터와 루트 디렉터리 트리 개체를 가리키는 포인터 정보를 커밋 개체에 넣어 저장한다.

※ 체크섬은 SHA-1을 사용해 구성된다.

이 작업을 마치고 나면 Git 저장소에는 다섯 개의 데이터 개체가 생긴다.

- 메타데이터와 루트 트리를 가리키는 포인터가 담긴 커밋 개체 하나
- 파일과 디렉터리 구조가 들어 있는 트리 개체 하나
- 각 파일에 대한 Blob 세 개

![커밋과 트리 데이터](/assets/img/2022-01-12/Git/Creating a New Branch/커밋과 트리 데이터.png)
_커밋과 트리 데이터_

파일을 수정하고 커밋하면 이전 커밋이 무엇인지도 저장한다.

![커밋과 이전 커밋](/assets/img/2022-01-12/Git/Creating a New Branch/커밋과 이전 커밋.png)
_커밋과 이전 커밋_

Git의 브랜치는 커밋 사이를 이동할 수 있는 포인터 같은 개념이다. Git은 기본적으로 master(or main) 브랜치를 만든다. 처음 커밋하면 이 master 브랜치가 생성된 커밋을 가리킨다. 이후 커밋을 만들면 브랜치는 자동으로 가장 마지막 커밋을 가리킨다.

## 새 브랜치 생성하기

---

브랜치를 하나 만들어 보자.

```bash
$ git branch testing
```

새로 만든 브랜치도 지금 작업하고 있던 마지막 커밋을 가리킨다.

![한 커밋 히스토리를 가리키는 두 브랜치](/assets/img/2022-01-12/Git/Creating a New Branch/한 커밋 히스토리를 가리키는 두 브랜치.png)
_한 커밋 히스토리를 가리키는 두 브랜치_

지금 작업 중인 브랜치가 무엇인지 Git은 어떻게 파악할까? 다른 VCS와는 달리 Git은 “HEAD”라는 특수한 포인터가 있다. 이 포인터는 지금 작업하는 로컬 브랜치를 가리킨다. 브랜치를 새로 만들었지만, Git은 아직 master 브랜치를 가리키고 있다. `git branch` 명령은 브랜치를 만들기만 하고 브랜치를 옮기지는 않기 때문이다.

![현재 작업 중인 브랜치를 가리키는 HEAD](/assets/img/2022-01-12/Git/Creating a New Branch/현재 작업 중인 브랜치를 가리키는 HEAD.png)
_현재 작업 중인 브랜치를 가리키는 HEAD_

`git log` 명령을 사용하면 HEAD가 어떤 커밋을 가리키고 있는지 확인할 수 있다.

## 브랜치 이동하기

---

`git switch` 명령으로 HEAD를 다른 브랜치로 이동시킬 수 있다. 한번 testing 브랜치로 옮겨보자.

```bash
$ git switch testing
$ git log --oneline
```

![HEAD는 testing 브랜치를 가리킴](/assets/img/2022-01-12/Git/Creating a New Branch/현재 작업 중인 브랜치를 가리키는 HEAD.png)
_HEAD는 testing 브랜치를 가리킴_

HEAD가 testing 브랜치를 가리키는 것을 알 수 있다.

testing 브랜치에서 파일 생성 후 커밋을 새로 해보자.

```bash
$ echo 'on testing branch' > test.rb
$ git add test.rb
$ git commit -m 'made a change in testing branch'
```

![HEAD가 가리키는 testing 브랜치가 새 커밋을 가리킴](/assets/img/2022-01-12/Git/Creating a New Branch/HEAD가 가리키는 testing 브랜치가 새 커밋을 가리킴.png)
_HEAD가 가리키는 testing 브랜치가 새 커밋을 가리킴_

새로 커밋해서 testing 브랜치는 앞으로 이동했다. 하지만 master 브랜치는 여전히 이전 커밋을 가리킨다. master 브랜치로 돌아가 보자.

```bash
$ git switch master
```

![HEAD가 Switch한 브랜치(master)로 이동함](/assets/img/2022-01-12/Git/Creating a New Branch/HEAD가 Switch한 브랜치(master)로 이동함.png)
_HEAD가 Switch한 브랜치(master)로 이동함_

방금 실행한 명령이 한 일은 두 가지다. master 브랜치가 가리키는 커밋을 HEAD가 워킹 디렉터리의 파일도 그 시점으로 되돌려 놓았다. 앞으로 커밋을 하면 다른 브랜치의 작업들과 별개로 진행되기 때문에 testing 브랜치에서 임시로 작업하고 원래 브랜치인 master로 돌아와서 하던 일을 계속할 수 있다.

> **브랜치를 이동하면 워킹 디렉토리의 파일이 변경된다**는 점을 기억해두어야 한다. 이전에 작업했던 브랜치로 이동하면 워킹 디렉토리의 파일은 그 브랜치에서 가장 마지막으로 했던 작업 내용으로 변경된다. 파일 변경시 문제가 있어 브랜치를 이동시키는게 불가능한 경우 Git은 브랜치 이동 명령을 수행하지 않는다.
> 

master 브랜치에서 파일을 생성하고 다시 커밋해보자.

```bash
$ echo 'on master branch' > test.rb
$ git add test.rb
$ git commit -m 'made other changes in master branch'
```

![갈라지는 브랜치](/assets/img/2022-01-12/Git/Creating a New Branch/갈라지는 브랜치.png)
_갈라지는 브랜치_

브랜치가 갈라져 각 브랜치에 있는 test.rb 파일은 서로 다른 파일이 된다.

`git log` 명령을 통해 현재 브랜치가 가리키고 있는 히스토리가 무엇이고 어떻게 갈라져 나왔는지 보여준다. `git log --oneline --graph --all` 이라고 실행하면 히스토리를 출력한다.

```bash
$ git log --oneline --graph --all
* c2b9e (HEAD, master) made other changes
| * 87ab2 (testing) made a change
|/
* f30ab add feature #32 - ability to add new formats to the
* 34ac2 fixed bug #1328 - stack overflow under certain conditions
* 98ca9 initial commit of my project
```

실제로 Git의 브랜치는 어떤 한 커밋을 가리키는 40글자의 SHA-1 체크섬 파일에 불과하기 때문에 마만들기도 쉽고 지우기도 쉽다. 새로 브랜치를 하나 만드는 것은 41바이트 크기의 파일(40자와 줄 바꿈 문자)을 하나 만드는 것에 불과하다.

브랜치가 필요할 때 프로젝트를 통째로 복사해야 하는 다른 VCS와 Git의 차이는 극명하다. 통째로 복사하는 작업은 수십 초에서 수십 분까지 걸린다. 그에 비해 Git은 순식간이다. 게다가 커밋을 할 때마다 이전 커밋의 정보를 저장하기 때문에 Merge할 때 어디서부터(Merge Base) 합쳐야 하는지 안다.
