---
title: '[Git] 리모트 저장소'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 18:36:00 +0900
categories: [Git, Getting Started]
tags: [git tag, Lightweight Tag, Annotated Tag, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

리모트 저장소를 알아야 다른 개발자들과 협업할 수 있다.리모트 저장소는 인터넷이나 네트워크 어딘가에 있는 저장소를 말한다. 저장소는 여러 개가 있을 수 있는데 어떤 저장소는 읽고 쓰기 모두 할 수 있고 어떤 저장소는 읽기만 가능하다. 간단히 말해서 다른 개발자들과 함께 협업한다는 것은 리모트 저장소를 관리하면서 데이터를 거기에 Push하고 Pull하는 것이다. 리모트를 관리한다는 것은 저장소를 추가, 삭제하는 것뿐만 아니라 브랜치를 관리하고 추적할지 말지 등을 관리하는 것을 말한다. 이번에는 리모트 저장소를 관리하는 방법에 대해 설명한다.

## 리모트 저장소 확인하기

---

`git remote` 명령으로 현재 프로젝트에 등록된 리모트 저장소를 확인할 수 있다. 저장소를 Clone하면 origin이라는 이름의 리모트 저장소가 자동으로 등록된다.

![Untitled](/assets/img/2022-01-12/Git/Working with Remotes/Untitled.png)

`-v` 옵션을 주어 단축 이름과 URL을 함께 볼 수 있다.

![Untitled](/assets/img/2022-01-12/Git/Working with Remotes/Untitled 1.png)

만약 리모트 저장소가 여러 개 있다면 이 명령은 등록된 전부를 보여준다. 여러 사람과 함께 작업하는 리모트 저장소가 여러 개라면 아래와 같은 결과를 얻을 수 도 있다.

![Untitled](/assets/img/2022-01-12/Git/Working with Remotes/Untitled 2.png)

이렇게 여러 리모트 저장소가 등록되어 있으면 다른 사람이 기여한 내용(Contributions)을 쉽게 가져올 수 있다. 어떤 저장소에는 Push 권한까지 제공하기도 하지만 일단 이 화면에서 Push 기능 권한까지는 확인할 수 없다.

## 리모트 저장소 추가하기

---

`git remote add <remote-name> <url>` 명령을 통해 리모트 저장소를 쉽게 추가할 수 있다.

![Untitled](/assets/img/2022-01-12/Git/Working with Remotes/Untitled 3.png)

## 리모트 저장소를 Pull하거나 Fetch하기

---

`git fetch <remote-name>` 명령을 통해 리모트 저장소에서 모든 브랜치 데이터를 받아올 수 있다.

저장소를 Clone하면 명령은 자동으로 리모트 저장소를 "origin"이라는 이름으로 추가한다. 그래서 나중에 `git fetch origin`을 실행하면 Clone 한 이후에 리모트 저장소에서 수정된 것을 모두 가져온다. `git fetch` 명령은 리모트 저장소의 데이터를 모두 로컬로 가져오지만, **자동으로 Merge하지는 않는다**. 그래서 우리가 로컬에서 하던 작업을 정리하고 나서 수동으로 Merge해야 한다.

이 번거로움을 해소하기 위해 있는 명령어가 git pull이다. 이 명령으로 **리모트 저장소 브랜치에서 데이터를 가져올 뿐만 아니라 자동으로 로컬 브랜치와 Merge해준다**.

## 리모트 저장소에 Push하기

---

프로젝트를 공유하고 싶을 때 Upstream 저장소에 Push할 수 있다. 이 명령은 `git push <remote-name> <branch-name>`으로 단순하다. master 브랜치를 origin 서버에 Push하려면 아래와 같은 명령을 쓰면 된다.

```bash
$ git push origin master
```

이 명령은 origin 저장소에 쓰기 권한이 있고, Clone하고 난 후 아무도 Upstream 저장소에 Push하지 않았을 때만 사용할 수 있다. 다시 말해서 Clone한 사람이 여러 명 있을 때, 다른 사람이 Push한 경우 나는 Push 할 수 없다. 먼저 다른 사람이 작업한 것을 가져와서 Merge한 후에 Push할 수 있다.

## 리모트 저장소 살펴보기

---

`git remote show <remote-name>` 명령으로 리모트 저장소의 구체적인 정보를 확인할 수 있다.

![Untitled](/assets/img/2022-01-12/Git/Working with Remotes/Untitled 4.png)

리모트 저장소의 URL과 추적 중인 브랜치를 출력한다. 이 명령은 `git pull` 명령을 실행할 때 master 브랜치와 Merge할 브랜치가 무엇인지 보여준다.

추후 프로젝트가 점점 커지면서 브랜치가 여러개가 되면 `git push` 명령을 실행할 때 어떤 브랜치가 어떤 브랜치로 Push되는지, 아직 로컬로 가져오지 않은 리모트 저장소의 브랜치는 어떤 것들이 있는지, 서버에서는 삭제됐지만 아직 가지고 있는 브랜치는 어떤 것인지, `git pull` 명령을 실행했을 때 자동으로 Merge할 브랜치는 어떤 것이 있는지 볼 수 있다.

## 리모트 저장소 이름을 바꾸거나 리모트 저장소를 삭제하기

---

`git remote rename <old-remote-name> <new-remote-name>` 명령으로 리모트 저장소의 이름을 변경할 수 있다. 예를 들어 example을 ex로 변경하려면 다음과 같이 하면 된다.

![Untitled](/assets/img/2022-01-12/Git/Working with Remotes/Untitled 5.png)

리모트 저장소를 삭제해야 한다면 `git remote rm` 명령을 사용한다.

![Untitled](/assets/img/2022-01-12/Git/Working with Remotes/Untitled 6.png)