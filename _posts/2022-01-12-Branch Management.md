---
title: '[Git] 브랜치 관리'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 19:34:00 +0900
categories: [Git, Branch]
tags: [git branch, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

어떻게 충돌을 해결했고 좀 더 확인해야 하는 부분은 무엇이고 왜 그렇게 해결했는지에 대해서 자세하게 기록한다. 자세한 기록은 나중에 이 Merge 커밋을 이해하는데 도움을 준다.

## 브랜치 관리

---

지금까지 브랜치를 만들고, Merge 하고, 삭제하는 방법에 대해서 살펴봤다. 브랜치를 관리하는 데 필요한 다른 명령도 살펴보자.

`git branch` 명령은 단순히 브랜치를 만들고 삭제하는 것이 아니다. 아무런 옵션 없이 실행하면 브랜치의 목록을 보여준다.

```bash
$ git branch
  iss53
* master
  testing
```

* 기호가 붙어 있는 `master` 브랜치는 현재 Switch 해서 작업 중인 브랜치를 나타낸다. 즉, 지금 수정한 내용을 커밋하면 `master` 브랜치에 커밋되고 포인터가 앞으로 한 단계 나아간다. `git branch -v` 명령을 실행하면 브랜치마다 마지막 커밋 메시지도 함께 보여준다.

```bash
$ git branch -v
  iss53   93b412c fix javascript issue
* master  7a98805 Merge branch 'iss53'
  testing 782fd34 add scott to the author list in the readmes
```

각 브랜치가 지금 어떤 상태인지 확인하기에 좋은 옵션도 있다. 현재 Switch 한 브랜치를 기준으로`--merged` 와 `--no-merged` 옵션을 사용하여 Merge 된 브랜치인지 그렇지 않은 브랜치인지 필터링해 볼 수 있다. `git branch --merged` 명령으로 이미 Merge 한 브랜치 목록을 확인한다.

```bash
$ git branch --merged
  iss53
* master
```

iss53 브랜치는 앞에서 이미 master 브랜치에 Merge 했기 때문에 목록에 나타난다. * 기호가 붙어 있지 않은 브랜치는 `git branch -d` 명령으로 삭제해도 된다. 이미 다른 브랜치와 Merge 했기 때문에 삭제해도 정보를 잃지 않기 때문이다(master에 iss53과 합쳐진 정보가 있음).

반대로 Switch한 브랜치에 Merge하지 않은 브랜치를 살펴보려면 `git branch --no-merged` 명령을 사용한다.

```bash
$ git branch --no-merged
  testing
```

`--merged` 옵션을 사용했을때 보이지 않던 testing 브랜치가 보인다. 아직 Merge하지 않은 커밋을 담고 있기 때문에 git branch -d 명령으로 삭제되지 않는다.

```bash
$ git branch -d testing
error: The branch 'testing' is not fully merged.
If you are sure you want to delete it, run 'git branch -D testing'.
```

Merge 하지 않은 브랜치를 강제로 삭제하려면 `-D` 옵션으로 삭제한다.

> 위에서 설명한 `--merged`, `--no-merged` 옵션을 사용할 때 커밋이나 브랜치 이름을 지정해주지 않으면 **현재 브랜치**를 기준으로 Merge 되거나 Merge 되지 않은 내용을 출력한다.
> 
> 
> 특정 브랜치를 기준으로 Merge 되거나 혹은 Merge 되지 않은 브랜치 정보를 살펴보려면 명령에 브랜치 이름을 지정해주면 된다. 예를 들어 master 브랜치에 아직 Merge되지 않은 브랜치를 살펴보려면 다음과 같은 명령을 실행한다.
> 
> ```bash
> $ git switch testing
> $ git branch --no-merged master
>   topicA
>   featureB
> ```
> 

## 브랜치 워크플로

---

브랜치를 생성하고 Merge 하는 것을 어디에 써먹어야 할까. 이 절에서는 유용한 몇 가지 워크플로를 살펴본다. 여기서 설명하는 워크플로를 개발에 적용하면 많은 도움이 될 것이다.

### Long-Running 브랜치

Git은 꼼꼼하게 3-way Merge를 사용하기 때문에 장기간에 걸쳐서 한 브랜치를 다른 브랜치와 여러 번 Merge 하는 것이 쉬운 편이다. 그래서 개발 과정에서 필요한 용도에 따라 브랜치를 만들어 두고 계속 사용할 수 있다. 그리고 정기적으로 브랜치를 다른 브랜치로 Merge 한다.

이런 접근법에 따라서 Git 개발자가 많이 선호하는 워크플로가 하나 있다. 배포했거나 배포할 코드만 master 브랜치에 Merge 해서 안정된 버전의 코드만 master 브랜치에 둔다. 개발을 진행하는 브랜치는 develop 이나 next 라는 이름으로 추가로 만들어 사용한다. 이 브랜치는 언젠가 안정 상태가 되겠지만, 항상 안정 상태를 유지해야 하는 것이 아니다. 테스트를 거쳐서 안정적이라고 판단되면 master 브랜치에 Merge 한다. 토픽 브랜치(앞서 살펴본 iss53 브랜치 같은 짧은 호흡 브랜치)에도 적용할 수 있는데, 해당 토픽을 처리하고 테스트해서 버그도 없고 안정적이면 그때 Merge 한다.

사실 우리가 얘기하는 것은 커밋을 가리키는 포인터에 관한 얘기다(커밋 포인터 생성/수정/분리/합치기 등). 개발 브랜치는 공격적으로 히스토리를 만들어 나아가고 안정 브랜치는 이미 만든 히스토리를 뒤따르며 나아간다.

![안정적인 브랜치일수록 커밋 히스토리가 뒤쳐짐](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2016.png)

안정적인 브랜치일수록 커밋 히스토리가 뒤쳐짐

백신 실험실에서 각 백신 약물 버전을 충분히 테스트한 후 안정된 약물을 각지에 전달하는 과정으로 보면 이해하기 쉽다.

![각 브랜치는 백신 실험실로, 각 커밋은 약물 버전으로 이해](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2017.png)

각 브랜치는 백신 실험실로, 각 커밋은 약물 버전으로 이해

코드를 여러 단계로 나누어 진행하므로 안정적으로 운영할 수 있게 된다. 프로젝트 규모가 크면 proposed 혹은 pu(proposed updates)라는 이름의 브랜치를 만들고 next나 master 브랜치에 아직 Merge 할 준비가 되지 않은 것을 일단 Merge 시킨다. 중요한 개념은 브랜치를 이용해 여러 단계에 걸쳐서 안정화해 나아가면서 충분히 안정화가 됐을 때 안정 브랜치로 Merge 한다는 점이다. 다시 말해서 Long-Running의 브랜치가 여러 개일 필요는 없지만 정말 유용하다는 점이다. 특히 규모가 크고 복잡한 프로젝트일수록 그 유용함이 더욱 빛나게 된다.

### 토픽 브랜치

토픽 브랜치는 프로젝트 크기에 상관없이 유용하다. 토픽 브랜치는 어떤 한 가지 주제나 작업을 위해 만든 짧은 호흡의 브랜치다. 다른 버전 관리 시스템에서는 이런 브랜치를 본 적이 없을 것이다. Git이 아닌 다른 버전 관리 도구에서는 브랜치를 하나 만드는 데 큰 비용이 든다. 그에 반해 Git은 비용이 부담되지 않아 개발자는 매우 일상적으로 브랜치를 만들고 Merge 하고 삭제한다.

앞서 사용한 iss53 이나 hotfix 브랜치가 토픽 브랜치다. 우리는 브랜치를 새로 만들고 어느 정도 커밋하고 나서 다시 master 브랜치에 Merge 하고 브랜치 삭제도 해 보았다. 보통 주제별로 브랜치를 만들고 각각은 독립돼 있기 때문에 매우 쉽게 컨텍스트 사이를 옮겨 다닐 수 있다. 묶음별로 나눠서 일하면 내용별로 검토하기에도, 테스트하기에도 더 편하다. 각 작업을 하루든 한 달이든 유지하다가 master 브랜치에 Merge 할 시점이 되면 순서에 관계없이 그때 Merge 하면 된다.

master 브랜치로 Switch 한 상태에서 어떤 작업을 한다고 해보자. 한 이슈를 처리하기 위해서 iss91 브랜치를 만들고 해당 작업을 한다. 같은 이슈를 다른 방법으로 해결해보고 싶을 때도 있다. iss91v2 브랜치를 만들고 다른 방법을 시도해 본다. 확신할 수 없는 아이디어를 적용해보기 위해 다시 master 브랜치로 되돌아가서 dumbidea 브랜치를 하나 더 만든다. 지금까지 설명한 커밋 히스토리는 아래 그림 같다.

![토픽 브랜치가 많음](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2018.png)

토픽 브랜치가 많음

이슈를 처리했던 방법 중 두 번째 방법인 iss91v2 브랜치가 괜찮아서 적용하기로 결정했다. 그리고 아이디어를 확신할 수 없었던 dumbidea 브랜치를 같이 일하는 다른 개발자에게 보여줬더니 썩 괜찮다는 반응을 얻었다. iss91 브랜치는(C5, C6 커밋도 함께) 버리고 다른 두 브랜치를 Merge 하면 아래 그림과 같이 된다.

![dumbidea와 iss91v2 브랜치를 Merge 하고 난 후의 히스토리 모습](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2019.png)

dumbidea와 iss91v2 브랜치를 Merge 하고 난 후의 히스토리 모습

[분산 환경에서의 Git](https://git-scm.com/book/ko/v2/ch00/ch05-distributed-git)에서 프로젝트를 Git으로 관리할 때 브랜치를 이용하여 만들 수 있는 여러 워크플로에 대해 살펴본다. 관련 부분을 살펴보면 프로젝트에 어떤 형태로 응용할수 있을 지 감이 올 것이다.

지금까지 한 작업은 전부 로컬에서만 처리한다는 것을 꼭 기억하자. 로컬 저장소에서만 브랜치를 만들고 Merge 했으며 서버와 통신을 주고받는 일은 없었다.

### 리모트 브랜치

리모트의 **Refs**는 리모트 저장소에 있는 포인터이자 레퍼런스이며 리모트 저장소에 있는 브랜치, 태그 등등을 의미한다. `git ls-remote <remote-name>` 명령으로 특정 리모트 혹은 모든 리모트 Refs를 조회할 수 있다. `git remote show <remote-name>` 명령은 특정 혹은 모든 리모트 브랜치의 정보를 보여준다. 리모트 Refs가 있지만 **보통은 리모트 트래킹 브랜치를 사용한다**.

**리모트 트래킹 브랜치**는 브랜치를 추적하는 레퍼런스이며 브랜치다. 리모트 트래킹 브랜치는 로컬에 있지만 임의로 움직일 수 없다. 리모트 서버에 연결할 때마다 리모트의 브랜치 업데이트 내용에 따라서 자동으로 갱신될 뿐이다. 리모트 트래킹 브랜치는 일종의 북마크라고 할 수 있다. 리모트 저장소에 마지막으로 연결했던 순간에 브랜치가 무슨 커밋을 가리키고 있는지를 나타낸다.

리모트 트래킹 브랜치의 이름은 `<remote-name>/<branch-name>` 형식으로 되어 있다. 예를 들어 리모트 저장소 origin의 master 브랜치를 보고 싶다면 origin/master 라는 이름으로 브랜치를 확인하면 된다. 다른 팀원과 함께 어떤 이슈를 해결해 나아갈 때 그 팀원이 iss53 브랜치를 서버로 Push했고 당신도 로컬에 iss53 브랜치가 있다고 가정하자. 이때 서버의 iss53 브랜치가 가리키는 커밋은 로컬에서 origin/iss53이 가리키는 커밋이다.

다소 헷갈릴 수 있으니 다음 예제를 살펴보자. git.ourcompany.com 이라는 Git 서버가 있고 이 서버의 저장소를 하나 Clone 하면 Git은 자동으로 origin 이라는 이름을 붙인다. origin으로부터 저장소 데이터를 모두 내려받고 master 브랜치를 가리키는 포인터를 만든다. 이 포인터는 origin/master 라고 부르고 멋대로 조종할 수 없다. 그리고 Git은 로컬의 master 브랜치가 리모트의 origin/master 브랜치를 가리키게 한다. 이제 이 master 브랜치에서 작업을 시작할 수 있다.

> **origin의 의미**
> 
> 
> 브랜치 이름으로 많이 사용하는 master 라는 이름이 괜히 특별한 의미를 가지는 게 아닌 것처럼 origin도 특별한 의미가 있는 것은 아니다. `git init` 명령이 자동으로 만들기 때문에 사용하는 이름인 master 와 마찬가지로 origin 도 `git clone` 명령이 자동으로 만들어주는 리모트 이름이다. `git clone -o mooyaho` 라고 옵션을 주고 명령을 실행하면 mooyaho/master 라고 사용자가 지정한 리모트 이름을 생성해준다.
> 

![Clone 이후 서버와 로컬의 master 브랜치](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2020.png)

Clone 이후 서버와 로컬의 master 브랜치

로컬 저장소에서 어떤 작업을 진행하고 있는데 다른 팀원이 git.ourcompany.com 서버에 Push하여 master 브랜치를 업데이트했다. 그러면 그 순간부터 팀원 간의 히스토리는 서로 달라지게 된다. 서버 저장소로부터 어떤 데이터도 주고받지 않아서 origin/master 포인터는 그대로다.

![로컬과 리모트 서버의 커밋 히스토리는 독립적임](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2021.png)

로컬과 리모트 서버의 커밋 히스토리는 독립적임

리모트 서버로부터 저장소 정보를 동기화하려면 `git fetch origin` 명령을 사용한다. 명령을 실행하면 origin 서버의 주소 정보(이 예에서는 git.ourcompany.com)를 찾아서 현재 로컬 저장소에는 없는 새로운 정보가 있으면 모두 내려받고, 받은 데이터를 로컬 저장소에 업데이트하고 나서 origin/master 포인터의 위치를 최신 커밋으로 이동시킨다.

![`git fetch` 명령은 리모트 브랜치 정보를 업데이트](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2022.png)

`git fetch` 명령은 리모트 브랜치 정보를 업데이트

리모트 저장소를 여러 개 운영하는 상황을 이해할 수 잇도록 개발용으로 사용할 Git 저장소를 팀 내부에 하나 추가해 보자. 이 저장소의 주소는 git.team1.ourcompany.com 이며 `git remote add` 명령으로 현재 작업 중인 프로젝트에 팀의 저장소를 추가한다. 이름을 teamone 으로 짓고 긴 서버 주소 대신 사용한다.

![서버를 리모트 저장소로 추가](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2023.png)

서버를 리모트 저장소로 추가

서버 추가 후 `git fetch teamone` 명령으로 teamone 서버의 최신 데이터를 모두 내려받는다. 그러나 명령을 실행해도 teamone 서버의 데이터는 모두 origin 서버에도 있는 것들이라서 아무것도 내려받지 않는다. 하지만 이 명령은 리모트 트래킹 브랜치 teamone/master가 teamone 서버의 master 브랜치가 가리키는 커밋을 가리키게 한다.

![teamone/master 의 리모트 트래킹 브랜치](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2024.png)

teamone/master 의 리모트 트래킹 브랜치

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

### 브랜치 추적

리모트 트래킹 브랜치를 로컬 브랜치로 Checkout 하면 자동으로 트래킹 브랜치가 만들어진다. 트래킹 하는 대상 브랜치를 Upstream 브랜치라고 부른다. 트래킹 브랜치는 리모트 브랜치와 연결되어 있는 로컬 브랜치다. 트래킹 브랜치에서 `git pull` 명령을 내리면 리모트 저장소로부터 데이터를 내려받아 연결된 리모트 브랜치와 자동으로 Merge 한다.

서버로부터 저장소를 Clone 하면 Git은 자동으로 로컬 master 브랜치를 리모트 origin/master 브랜치의 트래킹 브랜치로 만든다. 트래킹 브랜치를 직접 만들 수 있는데 리모트를 origin이 아닌 다른 리모트로 할 수도 있고, 브랜치도 master가 아닌 다른 브랜치로 추적할 수 있다.

`git switch -c <branch-name> <remote-name>/<branch-name>` 명령으로 간단히 트래킹 브랜치를 만들고 리모트 브랜치를 트래킹하도록 할 수 있다.

![리모트 저장소 tr의 fbaddfiles 브랜치를 추적하는 로컬 트래킹 브랜치 fbaddfiles 생성](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2025.png)

리모트 저장소 tr의 fbaddfiles 브랜치를 추적하는 로컬 트래킹 브랜치 fbaddfiles 생성

다음과 같이 `--track` 옵션을 사용하여 로컬 브랜치 이름을 자동으로 생성할 수도 있다.

![리모트 저장소 tr의 브랜치명을 참조해 feature_division 로컬 트래킹 브랜치 생성](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2026.png)

리모트 저장소 tr의 브랜치명을 참조해 feature_division 로컬 트래킹 브랜치 생성

이 명령은 매우 자주 쓰이므로 더 생략할 수 있다. 리모트 브랜치에는 존재하나 로컬 브랜치에는 존재하지 않는 경우 Git은 트래킹 브랜치를 자동으로 만들어 준다.

※ `git branch` 명령의 `-a` 옵션은 현재 리모트 저장소와 로컬 저장소의 모든 브랜치를 보여준다.

![로컬에는 없으나 리모트에는 존재하는 브랜치에 대한 트래킹 브랜치 자동 생성](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2027.png)

로컬에는 없으나 리모트에는 존재하는 브랜치에 대한 트래킹 브랜치 자동 생성

리모트 브랜치와 다른 이름으로 브랜치를 만들고 싶은 경우 로컬 브랜치의 이름을 다음과 같이 다르게 지정해보자.

![tr/gh-pages를 추적하는 mooyaho 트래킹 브랜치가 생성됨](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2028.png)

tr/gh-pages를 추적하는 mooyaho 트래킹 브랜치가 생성됨

이미 로컬에 존재하는 브랜치가 리모트의 특정 브랜치를 추적하게 하려면 `git branch` 명령에 `-u` 혹은 `--set-upstream-to` 옵션을 붙이면 된다.

![로컬 브랜치 mooyaho는 리모트 tr의 bisect 브랜치를 추적하게 됨](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2029.png)

로컬 브랜치 mooyaho는 리모트 tr의 bisect 브랜치를 추적하게 됨

`git branch -vv` 명령을 통해 mooyaho 브랜치가 tr/bisect 브랜치를 추적 중임을 알 수 있다.

![리모트 브랜치 bisect를 추적 중인 로컬 트래킹 브랜치 mooyaho](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2030.png)

리모트 브랜치 bisect를 추적 중인 로컬 트래킹 브랜치 mooyaho

> **Upstream 별명**
> 
> 
> 트래킹 브랜치를 설정했다면 트래킹 브랜치 이름을 `@{upstream}` 이나 `@{u}` 로 짧게 대체하여 사용할 수 있다. master 브랜치가 origin/master 브랜치를 추적하는 경우라면 `git merge origin/master` 명령과 `git merge @{u}` 명령을 똑같이 사용할 수 있다.
> 

트래킹 브랜치가 현재 어떻게 설정되어 있는지 확인하려면 `git branch` 명령에 `-vv` 옵션을 사용한다. 이 명령은 로컬 브랜치 목록과 로컬 브랜치가 추적하고 있는 리모트 브랜치도 함께 보여준다. 게다가 로컬 브랜치가 앞서가는지 뒤쳐지는지에 대한 내용도 보여준다.

![로컬 브랜치 상세 정보 조회](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2031.png)

로컬 브랜치 상세 정보 조회

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

### Pull 하기

`git fetch` 명령을 실행하면 서버에는 존재하지만 로컬에는 아직 없는 데이터를 받아와서 저장한다. 이때 워킹 디렉터리의 파일 내용은 변경되지 않고 그대로 남는다. 서버로부터 데이터를 가져와서 저장해두고 사용자가 직접 Merge 하도록 준비만 해둔다.

반면 `git pull` 명령은 `git fetch` 명령을 실행하고 나서 자동으로 `git merge` 명령을 수행하는 것 뿐이다. `git clone` 이나 `git switch` 명령을 실행하여 트래킹 브랜치가 설정되면 `git pull` 명령은 서버로부터 데이터를 가져와서 현재 로컬 브랜치와 서버의 트래킹 브랜치를 자동으로 Merge 한다.

### 리모트 브랜치 삭제

동료와 협업하기 위해 리모트 브랜치를 만들었다가 작업을 마치고 master 브랜치로 Merge 했다. 협업하는 데 사용했던 그 리모트 브랜치는 더 이상 필요하지 않기에 삭제할 수 있다. `git push` 명령에 `--delete` 혹은 `:` 옵션을 사용하여 리모트 브랜치를 삭제할 수 있다. serverfix 라는 리모트 브랜치를 삭제하려면 아래와 같이 실행한다.

```bash
$ git push origin --delete serverfix

or

$ git push origin : serverfix
To https://github.com/schacon/simplegit
 - [deleted]         serverfix
```

위 명령을 실행하면 서버에서 브랜치(커밋을 가리키는 포인터) 하나가 사라진다. Git의 가비지 컬렉터가 동작하지 않는 한 데이터는 사라지지 않기 때문에 종종 의도치 않게 삭제한 경우에도 커밋한 데이터를 되살릴 수도 있다. 그러나 만약 가비지 컬렉터가 가비지 컬렉팅 작업을 수행해 Refs와 연결되어 있지 않은 커밋들을 삭제한 상태라면 브랜치 또는 커밋을 복구할 수 없으므로 백업용 저장소를 함께 운용하는 것이 좋다.

### Rebase 하기

한 브랜치에서 다른 브랜치를 합치는 방법으로는 두 가지가 있다. 하나는 Merge 이고 다른 하나는 Rebase 이다. 이 절에서는 Rebase가 무엇인지, 어떻게 사용하는지, 좋은 점은 무엇인지, 어떤 상황에서 사용하고 어떤 상황에서는 사용하지 말아야 하는지 알아 본다.

앞에서 설명한 “Merge 기초” 절에서 살펴본 예제로 다시 돌아가 보자. 두 개의 나누어진 브랜치의 모습을 볼 수 있다.

 

![두 개의 브랜치로 나누어진 커밋 히스토리](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2032.png)

두 개의 브랜치로 나누어진 커밋 히스토리

이 두 브랜치를 합치는 가장 쉬운 방법은 앞에서 살펴본 대로 `git merge` 명령을 사용하는 것이다. 두 브랜치의 마지막 커밋 두 개(C3, C4)와 공통 조상(C2)을 참조하여 3-way Merge로 새로운 커밋을 만들어 낸다.

![나뉜 브랜치를 Merge 하기](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2033.png)

나뉜 브랜치를 Merge 하기

위와 비슷한 결과를 만드는 다른 방식을 알아보자. C3에서 변경된 사항을 Patch로 만들고 이를 다시 C4에 적용시키는 방법이 있다. Git에서는 이런 방식을 **Rebase** 라고 한다. `git rebase` 명령으로 한 브랜치에서 변경된 사항을 다른 브랜치에 적용할 수 있다.

위의 예제는 아래와 같은 명령으로 Rebase 한다.

```bash
$ git switch experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```

위 내용을 설명하자면 일단 두 브랜치가 나뉘기 전의 공통 커밋(C2)으로 이동하고 그 커밋부터 지금 Checkout 한 브랜치가 가리키는 커밋(C4)까지 diff를 차례로 만들어 어딘가에 임시로 저장해 놓는다. Rebase 할 브랜치(experiment)가 합쳐질 브랜치(master)가 가리키는 커밋을 가리키게 하고 아까 저장해 놓았던 변경사항을 차례로 적용한다.

![C4의 변경사항을 C3에 적용하는 Rebase 과정](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2034.png)

C4의 변경사항을 C3에 적용하는 Rebase 과정

그리고 master 브랜치를 C4’ 커밋에 Fast-forward 시킨다.

```bash
$ git switch master
$ git merge experiment
```

![master 브랜치를 C4’ 커밋에 Fast-forward 시키기](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2035.png)

master 브랜치를 C4’ 커밋에 Fast-forward 시키기

C4’로 표시된 커밋 내용은 Merge 예제에서 살펴본 C5 커밋 내용과 동일할 것이다. Merge 이든 Rebase 든 합치는 관점에서는 서로 다를 게 없다. Rebase가 좀 더 깔끔한 커밋 히스토리를 만든다는 차이만 있을 뿐이다. Rebase 한 브랜치의 Log를 살펴보면 히스토리가 선형적이다. 일을 병렬로 동시에 진행해도 Rebase 하고 나면 모든 작업이 차례로 수행된 것처럼 보이게 된다.

Rebase는 보통 리모트 브랜치에 커밋을 깔끔하게 적용하고 싶을 때 사용한다. 아마 이렇게 Rebase 하는 리모트 브랜치는 직접 관리하는 것이 아니라 그냥 참여하는 브랜치일 것이다. 메인 프로젝트에 Patch를 보낼 준비가 되면 하는 것이 Rebase 니까 브랜치에서 하던 일을 완전히 마치고 origin/master로 Rebase 한다. 이렇게 Rebase 하고 나면 프로젝트 관리자는 어떠한 통합작업도 할 필요가 없게 된다. 그저 master 브랜치를 Fast-forward 시키면 된다.

Rebase를 하든지, Merge를 하든지 최종 결과물은 같고 커밋 히스토리만 다르다는 점이 중요하다.

Rebase의 경우 브랜치의 변경사항을 순서대로 다른 브랜치에 적용하면서 합치고, Merge의 경우 두 브랜치의 최종 결과만을 가지고 합친다.

### Rebase 활용

Rebase는 단순히 브랜치를 합치는 것만 아니라 다른 용도로도 사용할 수 있다.

특정 토픽 브랜치에서 갈라져 나온 토픽 브랜치가 있는 히스토리를 생각해보자. server 브랜치를 만들어서 서버 기능을 추가하고 그 브랜치에서 다시 client 브랜치를 만들어 클라이언트 기능을 추가한다. 마지막으로 server 브랜치로 돌아가서 몇 가지 기능을 더 추가한다.

![특정 토픽 브랜치(server의 C3 커밋)에서 갈라져 나온 토픽 브랜치(client의 C8)](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2036.png)

특정 토픽 브랜치(server의 C3 커밋)에서 갈라져 나온 토픽 브랜치(client의 C8)

이때 테스트가 덜 진행된 server 브랜치는 그대로 두고 client 브랜치만 master 브랜치로 합치려는 상황을 생각해 보자. server와는 아무 관련이 없는 client 커밋은 C8, C9 이다. 이 두 커밋을 master 브랜치에 적용하기 위해 `--onto` 옵션을 사용하여 아래와 같은 명령을 실행한다.

```bash
$ git rebase --onto master server client
```

이 명령은 master 브랜치부터 server 브랜치와 client 브랜치의 공통 조상(C2)까지의 커밋을 client 브랜치에서 없애고 싶을 때 사용한다. client 브랜치에서만 변경된 패치를 만들어 master 브랜치에서 client 브랜치를 기반으로 새로 만들어 적용한다. 조금 복잡하긴 해도 꽤 쓸모 있다.

![특정 토픽 브랜치(C3)에서 갈라져 나온 토픽 브랜치(C8, C9)를 Rebase 하기](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2037.png)

특정 토픽 브랜치(C3)에서 갈라져 나온 토픽 브랜치(C8, C9)를 Rebase 하기

이제 master 브랜치로 돌아가서 client 브랜치 위치로 Fast-forward 시킨다.

```bash
$ git switch master
$ git merge client
```

![master 브랜치를 client 브랜치 위치로 이동](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2038.png)

master 브랜치를 client 브랜치 위치로 이동

server 브랜치에서 작업이 다 끝나면 `git rebase <basebranch-name> <topicbranch-name>` 라는 명령으로 바로 server 브랜치를 master 브랜치로 Rebase 할 수 있다. 이 명령은 토픽 브랜치(server)를 Checkout 하고 베이스 브랜치(master)에 Rebase 한다.

```bash
$ git rebase master server
```

![master 브랜치에 server 브랜치의 수정 사항 적용](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2039.png)

master 브랜치에 server 브랜치의 수정 사항 적용

server 브랜치의 수정사항을 master 브랜치에 적용했다.

이제 master 브랜치를 server 브랜치 위치로 Fast-forward 시킨다.

```bash
$ git switch master
$ git merge server
```

모든 것이 master 브랜치에 통합됐기 때문에 더 이상 필요하지 않은 client, server 브랜치는 삭제해도 된다.

```bash
$ git branch -d client
$ git branch -d server
```

![최종 커밋 히스토리](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2040.png)

최종 커밋 히스토리

브랜치를 삭제해도 커밋 히스토리에 위와 같이 같이 여전히 남아 있는 것을 확인할 수 있다.

### Rebase의 위험성

Rebase는 장점이 많은 기능이지만 단점이 없는 것은 아니니 조심해야 한다. 그 주의사항은 아래 한 문장으로 표현할 수 있다.

**이미 공개 저장소에 Push 한 커밋을 Rebase하지 말라**

이 지침만 지키면 Rebase를 하는 데 문제 될 것은 없을 것이다. 다만, 본인 부주의로 인해 이 주의사항을 지키지 않았을 경우 함께 협업하는 사람들로부터 수많은 삿대질과 욕을 배불리 먹어 장수하게 되는 기적이 올 것이다.

Rebase는 기존 커밋을 그대로 사용하는 것이 아니라 내용은 같지만 새로운 커밋을 만든다. 새 커밋을 서버에 Push 하고 동료 중 누군가가 그 커밋을 Pull 해서 작업을 한다고 하자. 그런데 그 커밋을 `git rebase` 로 바꿔서 Push 해버리면 동료가 다시 Push 했을 때 동료는 다시 Merge 해야 한다. 그리고 동료가 Merge 한 내용을 Pull 하면 내 코드는 엉망진창이 된다.

이미 저장소에 Push 한 커밋을 Rebase 하면 어떤 결과가 초래되는지 예제를 통해 알아보자. 중앙 저장소에서 Clone 하고 일부 수정을 하면 커밋 히스토리는 아래와 같이 된다.

![리모트 저장소를 Clone 하고 일부 수정함](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2041.png)

리모트 저장소를 Clone 하고 일부 수정함

이제 팀원 중 누군가 커밋, Merge 하고 나서 서버에 Push 한다. 이 리모트 브랜치를 Fetch, Merge 하면 히스토리는 아래와 같이 된다.

![리모트 저장소를 Fetch 한 후 Merge 함](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2042.png)

리모트 저장소를 Fetch 한 후 Merge 함

그런데 Push 했던 팀원은 Merge 한 일을 되돌리고 다시 Rebase 한다. 서버의 히스토리를 새로 덮어씌우려면 git push 명령에 `--force` 혹은 `-f` 옵션을 사용해야 한다. 이후 저장소에서 Fetch 하고 나면 아래 그림과 같은 상태가 된다.

![한 팀원이 다른 팀원이 의존하는 커밋을 없애고 Rebase 한 커밋을 다시 Push 함](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2043.png)

한 팀원이 다른 팀원이 의존하는 커밋을 없애고 Rebase 한 커밋을 다시 Push 함

이렇게 되면 히스토리가 짬뽕이 된다. `git pull`로 서버의 내용을 가져와서 Merge 하면 같은 내용의 수정사항을 포함한 Merge 커밋이 아래와 같이 만들어진다.

![같은 Merge를 다시 Merge하여 중복 커밋 발생](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2044.png)

같은 Merge를 다시 Merge하여 중복 커밋 발생

git log로 히스토리를 확인해보면 저자, 커밋 날짜, 메시지가 완전히 동일한 커밋 두 개(C4, C4’)가 있다. 이렇게 되면 혼란스럽다. C4와 C6는 포함되지 말았어야 할 커밋이다. 애초에 서버로 데이터를 보내기 전에 Rebase로 커밋을 선형으로 정리했어야 했다.

### Rebase 한 것을 다시 Rebase 하기

만약 이러한 상황에 처했을 때 유용한 Git 기능이 하나 있다. 어떤 팀원이 강제로 내가 한 일을 덮어썼다고 하자. 그러면 내가 했던 일이 무엇이고 덮어쓴 내용이 무엇인지 알아내야 한다.

커밋 SHA-1 체크섬 외에도 Git은 커밋에 Patch 할 내용으로 SHA-1 체크섬을 한 번 더 구한다. 이 값은 “**patch-id**” 라고 한다.

덮어쓴 커밋을 받아서 그 커밋을 기준으로 Rebase 할 때 Git은 원래 누가 작성한 코드인지 잘 찾아 낸다. 그래서 Patch가 원래대로 잘 적용된다.

예를 들어 앞서 살펴본 예제 중 한 팀원이 다른 팀원이 의존하는 커밋을 없애고 Rebase 한 커밋을 다시 Push  한 상황에서 Merge 하는 대신 git rebase teamone/master 명령을 실행하면 Git은 아래와 같은 작업을 한다.

- 현재 브랜치에만 포함된 커밋을 결정한다. (C2, C3, C4, C6, C7)
- Merge 커밋이 아닌 것을 결정한다. (C2, C3, C4)
- 이 중 Merge 할 브랜치에 덮어쓰이지 않은 커밋을 결정한다. (C2, C3)
- 결정한 커밋을 teamone/master 브랜치에 적용한다.

![강제로 덮어쓴 브랜치에 Rebase 하기](%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%203d20c626afd846218e6d3e7a4f369397/Untitled%2045.png)

강제로 덮어쓴 브랜치에 Rebase 하기

다시 Merge 한 결과는 엉망이였지만, Rebase 한 결과는 선형으로 깔끔하게 정리된 히스토리를 볼 수 있게 된다.

동료가 생성했던 C4와 C4' 커밋 내용이 완전히 같을 때만 이렇게 동작된다. 커밋 내용이 아예 다르거나 비슷하다면 커밋이 두 개 생긴다(같은 내용이 두 번 커밋될 수 있기 때문에 깔끔하지 않다).

`git pull` 명령을 실행할 때 옵션을 붙여서 `git pull --rebase` 로 Rebase 할 수도 있다. 물론 `git fetch` 와 `git rebase teamone/master` 이 두 명령을 직접 순서대로 실행해도 결과는 같다.

`git pull` 명령을 실행할 때 기본적으로 `--rebase` 옵션이 적용되도록 `pull.rebase` 설정을 추가할 수 있다. `git config --global pull.rebase true` 명령으로 추가한다.

Push 하기 전에 정리하려고 Rebase 하는 것은 괜찮다. 또 절대 공개하지 않고 혼자 Rebase 하는 경우도 괜찮다. **하지만, 이미 공개하여 사람들이 사용하는 커밋을 Rebase 하면 틀림없이 문제가 생긴다**.

나중에 후회하지 말고 `git pull --rebase` 로 문제를 미리 방지할 수 있다는 것을 같이 작업하는 동료와 모두 함께 공유하기 바란다.

### Rebase vs. Merge

Merge와 Rebase가 어떠한 기능을 하는지 여러 예제를 통해 간단히 살펴보았다. 지금쯤 이런 의문이 들 것으로 생각한다.

“둘 중 무엇을 쓰는 게 좋지?” 이 질문에 대한 답을 찾기 전에 히스토리의 의미에 대해서 잠깐 다시 생각해보자.

히스토리를 보는 관점 중에 하나는 **작업한 내용의 기록**으로 보는 것이 있다. 작업한 내용을 기록한 문서이고, 각 기록은 각각의 의미를 가지며, 변경할 수 없다. 이런 관점에서 커밋 히스토리를 변경한다는 것은 역사를 부정하는 꼴이 된다. 언제 무슨 일이 있었는지 기록에 대해 **거짓말**을 하게 되는 것이다. 이렇게 했을 때 지저분하게 수많은 Merge 커밋이 히스토리에 남게 되면 문제가 없을까? 역사는 후세를 위해 기록하고 보존해야 한다.

또 다른 관점으로는 히스토리를 **프로젝트가 어떻게 진행되었나에 대한 이야기**로도 볼 수 있다. 소프트웨어를 주의 깊게 편집하는 방법이나 세세한 작업 내용을 초벌부터 공개하고 싶지 않을 수 있다. 나중에 다른 사람에게 들려주기 좋게 Rebase 나 filter-branch 같은 도구로 프로젝트의 진행 이야기를 다듬으면 좋다.

Merge 나 Rebase 중 무엇이 나은가에 관한 질문은 다시 생각해봐도 답이 그리 간단치 않다. Git은 매우 강력한 도구이고 기능이 많아 히스토리를 잘 쌓을 수 있지만, 모든 팀과 모든 이가 처한 상황은 모두 다르다. 예제를 통해 Merge나 Rebase가 무엇이고 어떤 의미인지 배웠다. 이 둘을 어떻게 사용할지는 각자의 상황과 판단에 달렸다.

**일반적인 해답을 굳이 드리자면 로컬 브랜치에서 작업할 때는 히스토리를 정리하기 위해서 Rebase 할 수도 있지만, 리모트 등 어딘가에 Push로 내보낸 커밋에 대해서는 절대 Rebase 하지 말아야 한다는 것이다**.