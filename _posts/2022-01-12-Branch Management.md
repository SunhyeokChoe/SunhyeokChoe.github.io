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
