---
title: '[Git] 수정하고 저장소에 저장하기'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 17:24:00 +0900
categories: [Git, Getting Started]
tags: [Git Commit, Git Repository, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

Git 저장소를 하나 만들었고 Working Directory에 Checkout도 했다. 이제는 파일을 수정하고 파일의 스냅샷을 커밋해 보자. 파일을 수정하다가 저장하고 싶으면 스냅샷을 커밋한다.

워킹 디렉터리의 모든 파일은 크게 **Tracked(관리대상)**와 **Untracked(관리대상 아님)**로 나눈다. Tracked 파일은 또 **Unmodified(수정하지 않음)**와 **Modified(수정함)** 그리고 **Staged(커밋으로 저장소에 기록할)** 상태 중 하나이다.

Working Directory's file states

- Tracked
- Untracked

Tracked file's states

- Unmodified
- Modified
- Staged

Untracked 파일은 워킹 디렉터리에 있는 파일 중 스냅샷에도 Staging Area에도 포함되지 않은 파일이다.

처음 저장소를 Clone하면 모든 파일은 Tracked이면서 Untracked 상태이다. 파일을 Checkout하고 나서 아무것도 수정하지 않았기 때문에 그렇다.

마지막 커밋 이후 아직 아무것도 수정하지 않은 상태에서 어떤 파일을 수정하면 Git은 그 파일을 Modified 상태로 인식한다. 실제로 커밋을 하기 위해서는 이 수정한 파일을 Staged 상태로 만들고, Staged 상태의 파일을 커밋한다. 이런 라이프사이클을 계속 반복한다.

![The lifecycle of the status of your files](/assets/img/2022-01-12/Git/Recording Changes to the Repository/The lifecycle of the status of your files.png)
_The lifecycle of the status of your files_

## 파일의 상태 확인하기

---

파일의 상태를 확인하려면 보통 `git status` 명령을 사용한다. Clone한 후에 바로 이 명령을 실행하면 다음과 같은 메시지를 볼 수 있다.

```bash
$ git status
On Branch master
nothing to commit, working director yclean
```

위의 내용은 파일을 하나도 수정하지 않았다는 것을 말해준다. Tracked나 Modified 상태인 파일이 없다는 것이다. Untracked 파일은 아직 없어서 목록에 나타나지 않는다. 그리고 현재 작업중인 브랜치를 알려주며 서버의 같은 브랜치로부터 진행된 작업이 없는 것을 나타낸다. 기본 브랜치가 master이기 때문에 현재 브랜치 이름이 "master"로 나타난다.

## README 파일 만들기

---

```bash
$ echo 'My Project' > README
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
    
    README

nothing added to commit but untracked files present (use "git add" to track)
```

README 파일은 새로 만든 파일이기 때문에 `git status`를 실행하면 "Untracked files"에 들어 있다. 이것은 README 파일이 Untracked 상태라는 것을 말한다. Git은 Untracked 파일을 아직 스냅샷(커밋)에 넣어지지 않은 파일이라고 본다. 파일이 Tracked 상태가 되기 전까지는 Git은 절대 그 파일을 커밋하지 않는다. 그래서 업무중 실수로 생성하는 바이너리 파일 같은 것을 커밋하는 실수는 하지 않게 된다.

## 파일을 새로 추적하기

---

`git add` 명령으로 파일을 새로 추적할 수 있다. 아래 명령을 실행하면 Git은 README 파일을 추적한다.

```bash
$ git add README
```

`git status` 명령을 다시 실행하면 README 파일이 Tracked 상태이면서 커밋에 추가될 Staged 상태라는 것을 확인할  수 있다.

```bash
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:  README
```

"Changes to be committed"에 들어 있는 파일은 Staged 상태라는 것을 의미한다. 커밋하면 `git add`를 실행한 시점의 파일이 커밋되어 저장소 히스토리에 남는다. 앞에서 `git init` 명령을 실행한 후 그 다음 `git add <file>` 명령을 실행했던 걸 기억할 것이다. 이 명령을 통해 디렉터리에 있는 파일을 추적하고 관리하도록 한다. `git add` ****명령은 파일 또는 디렉터리의 경로를 아규먼트로 받는다. 디렉터리면 아래에 있는 모든 파일들까지 재귀적으로 추가한다.

## Modified 상태의 파일을 Stage하기

---

이미 Tracked 상태인 파일을 수정하는 방법을 알아보자. "CONTRIBUTING.md"라는 파일을 수정하고 나서 `git status` 명령을 다시 실행하면 결과는 아래와 같다.

```bash
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file: README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified: CONTRIBUTING.md
```

이 "CONTRIBUTING.md" 파일은 "Changes not staged for commit"에 있다. 이것은 수정한 파일이 Tracked 상태이지만 아직 Staged 상태는 아니라는 것이다. Staged 상태로 만들려면 `git add` 명령을 실행해야 한다. `git add` **명령은 파일을 새로 추적할 때도 사용하고 수정한 파일을 Staged 상태로 만들때도 사용한다. 또한, Merge할 때 충돌 난 상태의 파일을 Resolve 상태로 만들 때도 사용한다.** add의 의미는 프로젝트에 파일을 추가한다기보다는 다음 커밋에 추가한다고 받아들이는 게 좋다. `git add` 명령을 실행하여 "CONTRIBUTING.md" 파일을 Staged 상태로 만들고 `git status` 명령으로 결과를 확인해보자.

```bash
$ git add CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file: README
    modified: CONTRIBUTING.md
```

두 파일 모두 Staged 상태이므로 다음 커밋에 포함될 것이다. 하지만 아직 더 수정해야 한다는 것을 알게 되어 바로 커밋하지 못하는 상황이 되었다고 생각해보자. 이 상황에서 CONTIBUTING.md 파일을 열고 수정한다. 이제 커밋할 준비가 다 됐다고 생각할 테지만, Git은 그렇지 않다. `git status` 명령으로 파일의 상태를 다시 확인해 보자.

```bash
$ vim CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file: README
    modified: CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified: CONTRIBUTING.md
```

어라? CONTRIBUTING.md가 Staged 상태이면서 동시에 Unstaged 상태로 나온다. 어떻게 이런 일이 가능할까? `git add` 명령을 실행하면 Git은 파일을 바로 Staged 상태로 만든다. 지금 이 시점에서 커밋을 하면 `git commit` 명령을 실행하는 시점의 버전이 커밋되는 것이 아니라 마지막으로 `git add` 명령을 실행했을 때의 버전이 커밋된다. 그러니까 `git add` 명령을 실행한 후에 또 파일을 수정하면 `git add` 명령을 다시 실행해서 최신 버전을 Staged 상태로 만들어야 한다.

```bash
$ git add CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file: README
    modified: CONTRIBUTING.md
```

## 파일 상태를 짤막하게 확인하기

---

git status 명령으로 확인할 수 있는 내용이 좀 많아 보일 수 있다. 좀 더 간단하게 변경 내용을 보여주는 옵션이 있다. `git status -s` 또는 `git status --short` 옵션을 주면 현재 변경한 상태를 짤막하게 보여준다.

```bash
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

- {공백}M: 파일 수정 후 Staged 상태로 만들지 않은 파일
- M{공백}: 파일 수정 후 Staged 상태로 만든 파일
- MM:  파일 수정 후 Staged 상태로 만든 후 또 다시 수정하여 Staged이면서 Unstaged 상태인 파일
- A: 새로 생성한 파일
- ??: 아직 추적하지 않는 새 파일