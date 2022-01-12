---
title: '[Git] 리모트 브랜치'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 19:46:00 +0900
categories: [Git, Branch]
tags: [Git Tracking Branch, git remote, git branch, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

## 리모트 브랜치

---

리모트의 **Refs**는 리모트 저장소에 있는 포인터이자 레퍼런스이며 리모트 저장소에 있는 브랜치, 태그 등등을 의미한다. `git ls-remote <remote-name>` 명령으로 특정 리모트 혹은 모든 리모트 Refs를 조회할 수 있다. `git remote show <remote-name>` 명령은 특정 혹은 모든 리모트 브랜치의 정보를 보여준다. 리모트 Refs가 있지만 **보통은 리모트 트래킹 브랜치를 사용한다**.

**리모트 트래킹 브랜치**는 브랜치를 추적하는 레퍼런스이며 브랜치다. 리모트 트래킹 브랜치는 로컬에 있지만 임의로 움직일 수 없다. 리모트 서버에 연결할 때마다 리모트의 브랜치 업데이트 내용에 따라서 자동으로 갱신될 뿐이다. 리모트 트래킹 브랜치는 일종의 북마크라고 할 수 있다. 리모트 저장소에 마지막으로 연결했던 순간에 브랜치가 무슨 커밋을 가리키고 있는지를 나타낸다.

리모트 트래킹 브랜치의 이름은 `<remote-name>/<branch-name>` 형식으로 되어 있다. 예를 들어 리모트 저장소 origin의 master 브랜치를 보고 싶다면 origin/master 라는 이름으로 브랜치를 확인하면 된다. 다른 팀원과 함께 어떤 이슈를 해결해 나아갈 때 그 팀원이 iss53 브랜치를 서버로 Push했고 당신도 로컬에 iss53 브랜치가 있다고 가정하자. 이때 서버의 iss53 브랜치가 가리키는 커밋은 로컬에서 origin/iss53이 가리키는 커밋이다.

다소 헷갈릴 수 있으니 다음 예제를 살펴보자. git.ourcompany.com 이라는 Git 서버가 있고 이 서버의 저장소를 하나 Clone 하면 Git은 자동으로 origin 이라는 이름을 붙인다. origin으로부터 저장소 데이터를 모두 내려받고 master 브랜치를 가리키는 포인터를 만든다. 이 포인터는 origin/master 라고 부르고 멋대로 조종할 수 없다. 그리고 Git은 로컬의 master 브랜치가 리모트의 origin/master 브랜치를 가리키게 한다. 이제 이 master 브랜치에서 작업을 시작할 수 있다.

> **origin의 의미**
> 
> 
> 브랜치 이름으로 많이 사용하는 master 라는 이름이 괜히 특별한 의미를 가지는 게 아닌 것처럼 origin도 특별한 의미가 있는 것은 아니다. `git init` 명령이 자동으로 만들기 때문에 사용하는 이름인 master 와 마찬가지로 origin 도 `git clone` 명령이 자동으로 만들어주는 리모트 이름이다. `git clone -o mooyaho` 라고 옵션을 주고 명령을 실행하면 mooyaho/master 라고 사용자가 지정한 리모트 이름을 생성해준다.
> 

![Clone 이후 서버와 로컬의 master 브랜치](/assets/img/2022-01-12/Git/Remote Branches/Clone 이후 서버와 로컬의 master 브랜치.png)
_Clone 이후 서버와 로컬의 master 브랜치_

로컬 저장소에서 어떤 작업을 진행하고 있는데 다른 팀원이 git.ourcompany.com 서버에 Push하여 master 브랜치를 업데이트했다. 그러면 그 순간부터 팀원 간의 히스토리는 서로 달라지게 된다. 서버 저장소로부터 어떤 데이터도 주고받지 않아서 origin/master 포인터는 그대로다.

![로컬과 리모트 서버의 커밋 히스토리는 독립적임](/assets/img/2022-01-12/Git/Remote Branches/로컬과 리모트 서버의 커밋 히스토리는 독립적임.png)
_로컬과 리모트 서버의 커밋 히스토리는 독립적임_

리모트 서버로부터 저장소 정보를 동기화하려면 `git fetch origin` 명령을 사용한다. 명령을 실행하면 origin 서버의 주소 정보(이 예에서는 git.ourcompany.com)를 찾아서 현재 로컬 저장소에는 없는 새로운 정보가 있으면 모두 내려받고, 받은 데이터를 로컬 저장소에 업데이트하고 나서 origin/master 포인터의 위치를 최신 커밋으로 이동시킨다.

![git fetch 명령은 리모트 브랜치 정보를 업데이트](/assets/img/2022-01-12/Git/Remote Branches/git fetch 명령은 리모트 브랜치 정보를 업데이트.png)
_`git fetch` 명령은 리모트 브랜치 정보를 업데이트_

리모트 저장소를 여러 개 운영하는 상황을 이해할 수 잇도록 개발용으로 사용할 Git 저장소를 팀 내부에 하나 추가해 보자. 이 저장소의 주소는 git.team1.ourcompany.com 이며 `git remote add` 명령으로 현재 작업 중인 프로젝트에 팀의 저장소를 추가한다. 이름을 teamone 으로 짓고 긴 서버 주소 대신 사용한다.

![서버를 리모트 저장소로 추가](/assets/img/2022-01-12/Git/Remote Branches/서버를 리모트 저장소로 추가.png)
_서버를 리모트 저장소로 추가_

서버 추가 후 `git fetch teamone` 명령으로 teamone 서버의 최신 데이터를 모두 내려받는다. 그러나 명령을 실행해도 teamone 서버의 데이터는 모두 origin 서버에도 있는 것들이라서 아무것도 내려받지 않는다. 하지만 이 명령은 리모트 트래킹 브랜치 teamone/master가 teamone 서버의 master 브랜치가 가리키는 커밋을 가리키게 한다.

![teamone/master 의 리모트 트래킹 브랜치](/assets/img/2022-01-12/Git/Remote Branches/리모트 저장소 teamone의 master 리모트 트래킹 브랜치.png)
_teamone/master 의 리모트 트래킹 브랜치_

## Push 하기

---

로컬 브랜치를 서버에 보내려면 해당 서버에 쓰기 권한이 있어야 한다. 그리고 로컬 브랜치는 자동으로 리모트 저장소로 보내지는 것이 아닌 개발자가 직접 명시적으로 브랜치를 Push 해야 서버에 반영된다. 따라서 로컬 브랜치를 서버에 보내지 않고 로컬에만 두는 비공개 브랜치를 활용할 수도 있다. 또는 다른 사람과 협업하기 위한 토픽 브랜치만 전송할 수도 있다.

serverfix라는 브랜치를 다른 사람과 공유하기 위해 서버에 Push 해보자. 명령은 `git push <remote-name> <branch-name>` 이다.

```bash
$ git push origin serverfix
Counting objects: 24, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (24/24), 1.91 KiB | 0 bytes/s, done.
Total 24 (delta 2), reused 0 (delta 0)
To https://github.com/schacon/simplegit
 * [new branch]      serverfix -> serverfix
```

Git은 serverfix 브랜치 이름을 refs/heads/serverfix:refs/heads/serverfix로 확장한다. 이것은 로컬 브랜치 serverfix를 서버로 Push 하는데 리모트의 serverfix 브랜치로 업데이트 한다는 것을 의미한다. 나중에 refs/heads/의 뜻을 자세히 알아볼 것이기에 일단 넘어가도록 하자.

`git push origin serverfix:serverfix` 와 같은 명령을 통해 Push 하는 것도 같은 의미인데 이것은 “로컬의 serverfix 브랜치를 리모트 저장소의 serverfix 브랜치로 Push 하라” 라는 뜻이다. 이러한 명령은 로컬 브랜치의 이름과 리모트 서버의 브랜치 이름이 다를 때 사용한다.

리모트 저장소에 serverfix라는 이름 대신 다른 이름을 사용하려면 `git push origin serverfix:mooyaho` 처럼 사용하면 된다.

> **리모트 저장소에 접근할 때마다 암호를 매번 입력할 필요는 없다.**
HTTPS URL로 시작하는 리모트 저장소를 사용한다면 아마도 Push 나 Pull을 할 때 인증을 위한 사용자 이름이나 암호를 묻는 것을 볼 수 있다. Git이 이 정보를 서버로 전달해서 권한을 확인하기 위함이다.
> 
> 
> 매번 암호를 입력하는 작업이 번거롭다면 ”credential cache” 기능을 이용할 수 있다.
> 이 기능을 활성화하면 Git은 몇 분 동안 입력한 사용자 이름과 암호를 저장해둔다. 이 기능을 활성화하려면 `git config --global credential.helper cache` 명령을 실행하여 환경설정을 추가한다.
> 
> 이 기능이 제공하는 다른 옵션에 관한 자세한 설명은 [Credential 저장소](https://git-scm.com/book/ko/v2/ch00/_credential_caching)를 참고하자.
> 

저장소를 Fetch 하고 나서 서버에 있는 serverfix 브랜치에 접근할 때 origin/serverfix 라는 이름으로 접근할 수 있다.

```bash
$ git fetch origin
remote: Counting objects: 7, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0)
Unpacking objects: 100% (3/3), done.
From https://github.com/schacon/simplegit
 * [new branch]      serverfix    -> origin/serverfix
```

여기서 짚고 넘어가야 할 점이 있다. Fetch 명령을 통해 리모트 트래킹 브랜치를 내려받는다고 해서 로컬 저장소에 수정할 수 있는 브랜치가 자동으로 생성되는 것이 아니다. 다시 말해 serverfix 브랜치가 생기는 것이 아닌 그저 수정 불가능한 origin/serverfix 브랜치 포인터가 생기는 것이다.

새로 받은 브랜치의 내용을 Merge 하려면 `git merge origin/serverfix` 명령을 사용한다. 만약 Merge하지 않고 리모트 트래킹 브랜치에서 시작하는 새 브랜치를 로컬에 생성하려면 아래와 같은 명령을 사용한다.

```bash
$ git switch -c serverfix origin/serverfix
Switched to a new branch 'serverfix'
Branch 'serverfix' set up to track remote branch 'serverfix' from 'origin'.
```

origin/serverfix 에서 시작하고 수정할 수 있는 serverfix 라는 로컬 브랜치가 만들어졌다.

## 브랜치 추적

---

리모트 트래킹 브랜치를 로컬 브랜치로 Checkout 하면 자동으로 트래킹 브랜치가 만들어진다. 트래킹 하는 대상 브랜치를 Upstream 브랜치라고 부른다. 트래킹 브랜치는 리모트 브랜치와 연결되어 있는 로컬 브랜치다. 트래킹 브랜치에서 `git pull` 명령을 내리면 리모트 저장소로부터 데이터를 내려받아 연결된 리모트 브랜치와 자동으로 Merge 한다.

서버로부터 저장소를 Clone 하면 Git은 자동으로 로컬 master 브랜치를 리모트 origin/master 브랜치의 트래킹 브랜치로 만든다. 트래킹 브랜치를 직접 만들 수 있는데 리모트를 origin이 아닌 다른 리모트로 할 수도 있고, 브랜치도 master가 아닌 다른 브랜치로 추적할 수 있다.

`git switch -c <branch-name> <remote-name>/<branch-name>` 명령으로 간단히 트래킹 브랜치를 만들고 리모트 브랜치를 트래킹하도록 할 수 있다.

![리모트 저장소 tr의 fbaddfiles 브랜치를 추적하는 로컬 트래킹 브랜치 fbaddfiles 생성](/assets/img/2022-01-12/Git/Remote Branches/리모트 저장소 tr의 fbaddfiles 브랜치를 추적하는 로컬 트래킹 브랜치 fbaddfiles 생성.png)
_리모트 저장소 tr의 fbaddfiles 브랜치를 추적하는 로컬 트래킹 브랜치 fbaddfiles 생성_

다음과 같이 `--track` 옵션을 사용하여 로컬 브랜치 이름을 자동으로 생성할 수도 있다.

![리모트 저장소 tr의 브랜치명을 참조해 feature_division 로컬 트래킹 브랜치 생성](/assets/img/2022-01-12/Git/Remote Branches/리모트 저장소 tr의 브랜치명을 참조해 feature_division 로컬 트래킹 브랜치 생성.png)
_리모트 저장소 tr의 브랜치명을 참조해 feature_division 로컬 트래킹 브랜치 생성_

이 명령은 매우 자주 쓰이므로 더 생략할 수 있다. 리모트 브랜치에는 존재하나 로컬 브랜치에는 존재하지 않는 경우 Git은 트래킹 브랜치를 자동으로 만들어 준다.

※ `git branch` 명령의 `-a` 옵션은 현재 리모트 저장소와 로컬 저장소의 모든 브랜치를 보여준다.

![로컬에는 없으나 리모트에는 존재하는 브랜치에 대한 트래킹 브랜치 자동 생성](/assets/img/2022-01-12/Git/Remote Branches/로컬에는 없으나 리모트에는 존재하는 브랜치에 대한 트래킹 브랜치 자동 생성.png)

로컬에는 없으나 리모트에는 존재하는 브랜치에 대한 트래킹 브랜치 자동 생성

리모트 브랜치와 다른 이름으로 브랜치를 만들고 싶은 경우 로컬 브랜치의 이름을 다음과 같이 다르게 지정해보자.

![tr/gh-pages를 추적하는 mooyaho 트래킹 브랜치가 생성됨](/assets/img/2022-01-12/Git/Remote Branches/리모트 저장소 tr의 gh-pages 브랜치를 추적하는 mooyaho 로컬 트래킹 브랜치가 생성됨.png)
_tr/gh-pages를 추적하는 mooyaho 트래킹 브랜치가 생성됨_

이미 로컬에 존재하는 브랜치가 리모트의 특정 브랜치를 추적하게 하려면 `git branch` 명령에 `-u` 혹은 `--set-upstream-to` 옵션을 붙이면 된다.

![로컬 브랜치 mooyaho는 리모트 tr의 bisect 브랜치를 추적하게 됨](/assets/img/2022-01-12/Git/Remote Branches/로컬 브랜치 mooyaho는 리모트 tr의 bisect 브랜치를 추적하게 됨.png)

로컬 브랜치 mooyaho는 리모트 tr의 bisect 브랜치를 추적하게 됨

`git branch -vv` 명령을 통해 mooyaho 브랜치가 tr/bisect 브랜치를 추적 중임을 알 수 있다.

![리모트 브랜치 bisect를 추적 중인 로컬 트래킹 브랜치 mooyaho](/assets/img/2022-01-12/Git/Remote Branches/리모트 브랜치 bisect를 추적 중인 로컬 트래킹 브랜치 mooyaho.png)
_리모트 브랜치 bisect를 추적 중인 로컬 트래킹 브랜치 mooyaho_

> **Upstream 별명**
> 
> 
> 트래킹 브랜치를 설정했다면 트래킹 브랜치 이름을 `@{upstream}` 이나 `@{u}` 로 짧게 대체하여 사용할 수 있다. master 브랜치가 origin/master 브랜치를 추적하는 경우라면 `git merge origin/master` 명령과 `git merge @{u}` 명령을 똑같이 사용할 수 있다.
> 

트래킹 브랜치가 현재 어떻게 설정되어 있는지 확인하려면 `git branch` 명령에 `-vv` 옵션을 사용한다. 이 명령은 로컬 브랜치 목록과 로컬 브랜치가 추적하고 있는 리모트 브랜치도 함께 보여준다. 게다가 로컬 브랜치가 앞서가는지 뒤쳐지는지에 대한 내용도 보여준다.

![로컬 브랜치 상세 정보 조회](/assets/img/2022-01-12/Git/Remote Branches/로컬 브랜치 상세 정보 조회.png)
_로컬 브랜치 상세 정보 조회_

위의 결과를 보면 master 브랜치는 origin/master 리모트 브랜치를 추적하고 있다는 것을 알 수 있고 “ahead” 표시를 통해 로컬 브랜치가 커밋 1개 앞서 있다(리모트 브랜치에는 없는 커밋이 로컬에는 존재)는 것을 알 수 있다.

로컬 브랜치 중 mooyaho 브랜치는 bisect 이라는 tr 리모트 서버의 브랜치를 추적하고 있으며 커밋 3개 앞서 있고 동시에 커밋 16개 뒤쳐져 있다. 어떤 의미냐면 mooyaho 브랜치에서 서버로 보내지 않은 커밋이 1개, 서버의 브랜치에서 로컬 브랜치로 Merge 하지 않은 커밋이 16개 있다는 말이다.

br_version2, iss53, testing 브랜치는 추적하는 리모트 브랜치가 없는 상태이다.

여기서 중요한 점은 **명령을 실행한 시점의 결과는 마지막으로 데이터를 서버에서 가져온(Fetch or Pull) 시점을 바탕으로 계산한다는 점**이다. 단순히 이 명령만으론 서버의 최신 데이터를 반영하지는 않으며 로컬에 저장된 서버의 캐시 데이터를 사용한다. 현재 시점에서 최신 데이터로 추적 상황을 알아보려면 먼저 서버로부터 최신 데이터를 받아온 후에 추적 상황을 확인해야 한다. 아래처럼 세미콜론을 통해 두 명령을 이어서 사용하는 것이 적당하겠다.

```bash
$ git fetch --all; git branch -vv
```

> 여기서 세미콜론(;)은 git의 고유 명령이 아닌 Unix 체계의 명령이다.
세미콜론 앞의 명령어 실행을 마칠 때까지 기다린 후 이후의 명령어를 수행한다. 이는 동기식으로 진행되므로 두 명령어가 겹쳐 실행되는 경우는 없다.
그리고 앞 명령어가 실패해도 다음 명령어가 실행된다.

이와 유사한 명령이 있는데 기능은 다음과 같다.
& → 앞의 명령어를 비동기로 실행하고 동시에 뒤의 명령어를 실행
&& → 앞 명령어가 성공했을 때 다음 명령어 실행
> 

## Pull 하기

---

`git fetch` 명령을 실행하면 서버에는 존재하지만 로컬에는 아직 없는 데이터를 받아와서 저장한다. 이때 워킹 디렉터리의 파일 내용은 변경되지 않고 그대로 남는다. 서버로부터 데이터를 가져와서 저장해두고 사용자가 직접 Merge 하도록 준비만 해둔다.

반면 `git pull` 명령은 `git fetch` 명령을 실행하고 나서 자동으로 `git merge` 명령을 수행하는 것 뿐이다. `git clone` 이나 `git switch` 명령을 실행하여 트래킹 브랜치가 설정되면 `git pull` 명령은 서버로부터 데이터를 가져와서 현재 로컬 브랜치와 서버의 트래킹 브랜치를 자동으로 Merge 한다.

## 리모트 브랜치 삭제

---

동료와 협업하기 위해 리모트 브랜치를 만들었다가 작업을 마치고 master 브랜치로 Merge 했다. 협업하는 데 사용했던 그 리모트 브랜치는 더 이상 필요하지 않기에 삭제할 수 있다. `git push` 명령에 `--delete` 혹은 `:` 옵션을 사용하여 리모트 브랜치를 삭제할 수 있다. serverfix 라는 리모트 브랜치를 삭제하려면 아래와 같이 실행한다.

```bash
$ git push origin --delete serverfix

or

$ git push origin : serverfix
To https://github.com/schacon/simplegit
 - [deleted]         serverfix
```

위 명령을 실행하면 서버에서 브랜치(커밋을 가리키는 포인터) 하나가 사라진다. Git의 가비지 컬렉터가 동작하지 않는 한 데이터는 사라지지 않기 때문에 종종 의도치 않게 삭제한 경우에도 커밋한 데이터를 되살릴 수도 있다. 그러나 만약 가비지 컬렉터가 가비지 컬렉팅 작업을 수행해 Refs와 연결되어 있지 않은 커밋들을 삭제한 상태라면 브랜치 또는 커밋을 복구할 수 없으므로 백업용 저장소를 함께 운용하는 것이 좋다.
