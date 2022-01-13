---
title: '[Git] 분산 환경에서 프로젝트에 기여하기'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-13 18:20:58 +0900
categories: [Git, Workflow]
tags: [Workflow, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

기여하는 방식에 영향을 끼치는 변수가 다음과 같이 몇 가지 있다.

- 활발히 기여하는 개발자의 수가 몇인지
- 선택한 워크플로가 무엇인지
- 각 개발자에게 접근 권한을 어떻게 부여했는지
- 외부에서도 기여할 수 있는지 (접근 권한)

첫 번째로 살펴볼 변수는 **활발히 활동하는 개발자의 수**이다. 이 활발한 개발자에 대한 기준은 얼마나 잦은 빈도로 코드를 쏟아내 기여하는가이다. 대부분 둘, 셋 정도의 개발자가 하루에 몇 번 커밋을 하고 활발하지 않은 프로젝트는 더 띄엄띄엄 할 것이다. 하지만, 아주 큰 프로젝트는 수백, 수천 명의 개발자가 하루에도 수십, 수백 개의 커밋을 만들어 낸다. 개발자가 많으면 많을수록 코드를 깔끔하게 적용하거나 Merge 하기 어려워진다. 어떤 커밋은 다른 개발자가 이미 기여한 내용일 경우 불필요해지기도 하고 때론 서로 충돌이 발생한다. 어떻게 해야 코드를 최신으로 유지하면서 원하는 대로 수정할 수 있을까?

두 번째 변수는 **프로젝트에서 선택한 워크플로가 무엇인가**이다. 예시는 다음과 같다.

- 모든 개발자가 메인 저장소에 쓰기 권한을 갖는 중앙집중형 방식인가?
- 프로젝트에 모든 Patch를 검사하고 통합하는 관리자가 따로 있는가?
- 모든 수정사항을 개발자끼리 검토하고 승인하는가?
- 자신이 그저 돕는게 아니라 어떤 책임을 맡고 있는가?
- 중간 관리자가 있어서 그들에게 먼저 알려야 하는가?

세 번째 변수는 **접근 권한**이다. 프로젝트에 쓰기 권한이 있어서 직접적으로 수정이 가능한 경우나 읽기만 가능한 경우에 따라서 프로젝트에 기여하는 방식이 매우 달라진다. 예시는 다음과 같다.

- 쓰기 권한이 없다면 어떻게 수정 사항을 프로젝트에 반영할 수 있을까?
- 수정사항을 적용하는 정책이 프로젝트에 있는가?
- 얼마나 많은 시간을 프로젝트에 할애하는가?
- 얼마나 자주 기여하는가?

이런 저런 상황에 따라 프로젝트에 기여하는 방식과 워크플로가 달라진다. 간단한 것부터 복잡한 것까지 예제를 통해 각 상황을 살펴보면 실제 프로젝트에 필요한 워크플로를 선택하는 데 도움이 될 것이다.

## 커밋 가이드라인

---

프로젝트에 기여하기에 앞서 먼저 커밋 메시지 형식에 대한 주의사항을 알아보자. 커밋 메시지를 잘 작성하는 가이드라인을 알아두면 다른 개발자와 협업하는 데 도움이 많이 된다. 참고할 만한 좋은 팁들은 공식문서 [SubmittingPatches](https://github.com/git/git/blob/master/Documentation/SubmittingPatches)에 있다.

무엇보다도 먼저 공백문자를 깨끗하게 정리하고 커밋해야 한다. Git은 공백문자를 검사해볼 수 있는 간단한 명령을 제공한다. 커밋을 하기 전에 `git diff --check` 명령으로 공백문자에 대한 오류를 확인할 수 있다.

![git diff --check 결과](/assets/img/2022-01-13/Git/Contributing to a Project/git diff --check 결과.png)
_`git diff --check` 결과_

커밋 하기 전에 공백문자를 검사하면 의미없는 공백이 불필요하게 커밋되는 것을 막을 수 있다.

그리고 각 커밋은 논리적으로 구분되는 Changeset이다. 최대한 수정사항을 한 주제로 요약할 수 있어야 하고 여러 가지 이슈에 대한 수정사항을 하나의 커밋에 담지 않아야 한다. 작업 내용을 최대한 분할하고, 각 커밋마다 적절한 메시지를 작성한다. 같은 파일의 다른 부분을 수정하는 경우 `git add -patch` 명령을 통해 한 부분씩 나누어 Stage 해야 한다. 여러 번 나누어 커밋하면 다른 동료가 수정된 코드를 확인해야 할 때 코드 이해가 훨씬 수월해진다.

마지막으로 명심해야 할 점은 커밋 메시지 구성이다. 좋은 커밋 메시지를 작성하는 습관은 Git을 사용하는 데 많은 도움이 된다. 일반적으로 커밋 메시지를 작성할 때 사용하는 규칙이 있으며 다음과 같다.

- 첫 라인에 50자가 넘지 않는 아주 간략한 메시지를 적어 해당 커밋을 요약한다.
- 다음 한 라인은 비우고 그 다음 라인부터 커밋을 자세히 설명한다.
예를 들어 Git 개발 프로젝트에서는 개발 동기와 구현 상황의 제약 조건이나 상황 등을 자세하게 요구한다.
- 현재형 표현을 사용하는 것이 좋다. 명령문으로 시작하는 것도 좋은 방법이다.
예를 들어 “I added tests for (테스트를 추가함)” 보다는 “Add tests for (테스트 추가)”가 적합하다.

아래 내용은 [Tim Pope가 작성한 커밋 메시지 템플릿](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)이다.

```
영문 50글자 이하의 간략한 수정 요약

자세한 설명. 영문 72글자 이상이 되면
라인 바꿈을 하고 이어지는 내용을 작성한다.
특정 상황에서는 첫 번째 라인이 이메일
메시지의 제목이 되고 나머지는 메일
내용이 된다. 빈 라인은 본문과 요약을
구별해주기에 중요하다(본문 전체를 생략하지 않는 한).

이어지는 내용도 한 라인 띄우고 쓴다.

  - 목록 표시도 사용할 수 있다.

  - 보통 '-' 나 '*' 표시를 사용해서 목록을 표현하고
    표시 앞에 공백 하나, 각 목록 사이에는 빈 라인
    하나를 넣는데, 이건 상황에 따라 다르다.
```

메시지를 이렇게 작성하면 함께 일하는 사람은 물론이고 본인에게도 매우 유용하다. Git 개발 프로젝트에는 잘 쓰인 커밋 메시지가 많으므로 적절한 프로젝트를 내려받아서 `git log --no-merges` 명령으로 꼭 살펴보기를 권한다.

## 비공개 속규모 팀

---

두 세 명으로 이루어진 비공개 프로젝트가 가장 간단한 프로젝트일 것이다. “비공개” 라고 함은 소스코드가 외부에서 접근할 수 없게끔 공개되지 않은 것을 말한다.

이런 환경에서는 보통 Subversion 같은 중앙집중형 버전 관리 시스템에서 사용하던 방식을 사용한다. 물론 Git의 오프라인 커밋 기능이나 브랜치 Merge 기능을 이용하긴 하지만 크게 다르지 않다.

가장 큰 차이점은 서버가 아닌 **클라이언트 쪽에서 Merge** 한다는 점이다. 두 개발자가 저장소를 공유하는 시나리오를 살펴보자. 개발자인 John는 저장소를 Clone 하고 파일을 수정하고 나서 로컬에 커밋한다.

※ 예제에서 Git이 출력하는 메시지 템플릿 중 일부는 ‘...’로 줄이고 생략했다.

```bash
# John 컴퓨터
$ git clone john@githost:simplegit.git
Cloning into 'simplegit'...
...
$ cd simplegit/
$ vim lib/simplegit.rb
$ git commit -am 'remove invalid default value'
[master 738ee87] remove invalid default value
 1 files changed, 1 insertions(+), 1 deletions(-)
```

개발자인 Jessica도 저장소를 Clone 하고 나서 파일을 하나 새로 추가하고 커밋한다.

```bash
# Jessica 컴퓨터
$ git clone jessica@githost:simplegit.git
Cloning into 'simplegit'...
...
$ cd simplegit/
$ vim TODO
$ git commit -am 'add reset task'
[master fbff5bc] add reset task
 1 files changed, 1 insertions(+), 0 deletions(-)
```

Jessica는 서버에 커밋을 Push 한다.

```bash
# Jessica 컴퓨터
$ git push origin master
...
To jessica@githost:simplegit.git
   1edee6b..fbff5bc  master -> master
```

Push 명령을 실행하고 난 결과 중 마지막 줄은 유용한 정보를 보여준다. 마지막 줄의 기본 형식은 `<oldref>..<newref> fromref -> toref` 이다. 각 단어의 의미는 다음과 같다.

- `oldref` : 이전 레퍼런스 해시
- `newref` : 새 레퍼런스 해시
- `fromref` : Push 명령에서 사용한 로컬 레퍼런스 이름
- `toref` : Push로 업데이트한 리모트 레퍼런스 이름

이어지는 내용에서 지금 설명한 Push 명령 출력 결과가 여러번 등장하므로 위같은 메시지 포멧을 이해하고 있으면 다양한 상태에서 정확하게 어떤 일이 벌어지는가를 쉽게 이해할 수 있게 된다. 자세한 내용은 공식문서 [git-push](https://git-scm.com/docs/git-push)를 참고한다.

다시 예제 내용으로 돌아와 John도 내용을 변경하고 커밋을 만든 후 서버로 커밋을 Push 하려 한다.

```bash
# John 컴퓨터
$ git push origin master
To john@githost:simplegit.git
 ! [rejected]        master -> master (non-fast forward)
error: failed to push some refs to 'john@githost:simplegit.git'
```

Jessica의 Push한 내용으로 인해, John의 커밋은 서버에서 거절된다. Subversion을 사용했던 사람은 이 부분을 이해하는 것이 중요하다. 같은 파일을 수정한 것도 아닌데 왜 Push가 거절되는 걸까? Subversion에서는 서로 다른 파일을 수정하는 이런 Merge 작업은 자동으로 서버가 처리한다. 하지만 Git은 **로컬에서 먼저 Merge** 해야 한다. 다시 말해 John은 Push 하기 전에 Jessica가 수정한 커밋을 Fetch 하고 Merge 해야 한다는 말이다. 왜 먼저 원격 저장소를 Fetch를 해야 할까? John의 로컬 저장소의 `master` 브랜치가 원격 저장소의 `origin/master` 브랜치보다 커밋이 하나 뒤쳐져 있기 때문에 Push 를 하면 덮어써야만 하는 형태가 되기 때문에 Git은 실수로 남의 작업물에 덮어쓰는 불상사를 막을 뿐이다 (덮어쓰는 형태로 강제로 Push 할 순 있다. `git push -f <remote-name> <branch name>` 명령을 사용하면 된다).

이를 위해 John은 Jessica의 작업 내용을 아래와 같이 Fetch 한다. 아래 명령은 Jessica의 작업 내용을 내려받긴 하지만 Merge 까지 하지는 않는다.

```bash
$ git fetch origin
...
From john@githost:simplegit
 + 049d078...fbff5bc  master -> origin/master
```

Fetch 하고 나면 John의 로컬 저장소는 아래와 같이 된다.

![Fetch 하고 난 John의 저장소](/assets/img/2022-01-13/Git/Contributing to a Project/Fetch 하고 난 John의 저장소.png)
_Fetch 하고 난 John의 저장소_

Fetch 한 작업 내용을 로컬 브랜치에 Merge 하자.

```bash
$ git merge origin/master
Merge made by the 'recursive' strategy.
 TODO |    1 +
 1 files changed, 1 insertions(+), 0 deletions(-)
```

Merge 후 John의 브랜치는 아래와 같은 상태가 된다.

![origin/master 브랜치를 Merge 하고 난 후 John의 저장소](/assets/img/2022-01-13/Git/Contributing to a Project/origin master 브랜치를 Merge 하고 난 후 John의 저장소.png)
_`origin/master` 브랜치를 Merge 하고 난 후 John의 저장소_

3-Way 방식으로 새 커밋을 생성해 Merge 된 것을 알 수 있다. 자신의 코드와 내려받은 코드가 정상 작동됨을 확인한 후 원격 저장소에 Push 한다.

```bash
$ git push origin master
...
To john@githost:simplegit.git
   fbff5bc..72bbc59  master -> master
```

이제 John의 저장소는 아래와 같이 되었다.

![Push 하고 난 후 John의 저장소](/assets/img/2022-01-13/Git/Contributing to a Project/Push 하고 난 후 John의 저장소.png)
_Push 하고 난 후 John의 저장소_

리모트 트래킹 브랜치(`origin/master`)가 새로 생성된 커밋을 가리키도록 업데이트 되었다.

이번에 Jessica는 토픽 브랜치를 하나 생성한다. `issue54` 브랜치를 만들고 세 번에 걸쳐서 커밋한다. 아직 John의 커밋을 Fetch 하지 않은 상황이기 때문에 히스토리는 아래와 같은 상황이 된다.

![Jessica의 토픽 브랜치](/assets/img/2022-01-13/Git/Contributing to a Project/Jessica의 토픽 브랜치.png)
_Jessica의 토픽 브랜치_

Jessica는 John이 새로 Push 했다는 것을 알게 되어 하던 작업을 멈추고 John의 작업 내용을 살펴보려고 한다. 하지만 아직 Jessica는 John의 변경사항을 가지고 있지 않은 상태이다. 아래 명령으로 John이 Push 한 커밋을 모두 내려받는다.

```bash
# Jessica 컴퓨터
$ git fetch origin
...
From jessica@githost:simplegit
   fbff5bc..72bbc59  master -> origin/master
```

Jessica의 저장소는 아래와 같은 상태가 된다.

![John의 커밋을 Fetch 한 후 Jessica의 저장소](/assets/img/2022-01-13/Git/Contributing to a Project/John의 커밋을 Fetch 한 후 Jessica의 저장소.png)
_John의 커밋을 Fetch 한 후 Jessica의 저장소_

이제 `orgin/master` 와 Merge 할 차례다. Jessica는 토픽 브랜치에서의 작업을 마치고 어떤 내용이 Merge 되는지 `git log` 명령으로 확인한다.

```bash
$ git log --no-merges issue54..origin/master
commit 738ee872852dfaa9d6634e0dea7a324040193016
Author: John Smith <jsmith@example.com>
Date:   Fri May 29 16:01:27 2009 -0700

   remove invalid default value
```

`issue54..origin/master` 문법은 히스토리를 검색할 때 뒤의 브랜치(`origin/master`)에 속한 커밋 중 앞의 브랜치(`issue54`)에 속하지 않은 커밋을 검색하는 문법이다. 자세한 내용은 [범위로 커밋 가리키기](https://git-scm.com/book/ko/v2/ch00/_commit_ranges)에서 다룬다.

앞의 명령에 따라 히스토리를 검색한 결과 John이 생성하고 Jessica가 Merge 하지 않은 커밋을 하나 찾았다. `origin/master` 브랜치를 Merge 하게 되면 검색된 커밋 하나가 로컬 작업에 Merge 될 것이다.

Merge 할 내용을 확인한 Jessica는 자신이 작업한 내용과 John이 Push 한 작업(`origin/master`)을 `master` 브랜치에 Merge 하고 Push 한다.

`issue54` 토픽 브랜치에 쌓은 모든 내용을 합치려면, 우선 `master` 브랜치로 이동해야 한다.

```bash
$ git switch master
Switched to branch 'master'
Your branch is behind 'origin/master' by 2 commits, and can be fast-forwarded.
```

Jessica는 먼저 `issue54` 브랜치를 Merge 한다(`origin/master`, `issue54` 둘 중 아무거나 먼저 Merge 해도 된다).

```bash
$ git merge issue54
Updating fbff5bc..4af4298
Fast forward
 README           |    1 +
 lib/simplegit.rb |    6 +++++-
 2 files changed, 6 insertions(+), 1 deletions(-)
```

보다시피 Fast-forward Merge 이기 때문에 명령 실행 결과는 별 문제가 없다. `origin/master`에 쌓여있던 John의 작업 내용을 다음과 같이 실행하여 Jessica는 Merge 작업을 완료할 수 있다.

```bash
$ git merge origin/master
Auto-merging lib/simplegit.rb
Merge made by the 'recursive' strategy.
 lib/simplegit.rb |    2 +-
 1 files changed, 1 insertions(+), 1 deletions(-)
```

Merge 가 잘 되면 아래와 같은 상태가 된다.

![John의 커밋을 Merge 후 Jessica의 저장소](/assets/img/2022-01-13/Git/Contributing to a Project/John의 커밋을 Merge 후 Jessica의 저장소.png)
_John의 커밋을 Merge 후 Jessica의 저장소_

`origin/master` 브랜치가 Jessica의 `master` 브랜치로 나아갈(reachable) 수 있기 때문에 Push는 성공한다(물론 John이 그 사이에 Push 하지 않았다면).

이제 원격 저장소에 Push 한다.

```bash
$ git push origin master
...
To jessica@githost:simplegit.git
   72bbc59..8059c15  master -> master
```

두 개발자의 커밋을 성공적으로 Merge 한 결과는 아래와 같다.

![Jessica가 서버로 Push 하고 난 후의 저장소](/assets/img/2022-01-13/Git/Contributing to a Project/Jessica가 서버로 Push 하고 난 후의 저장소.png)
_Jessica가 서버로 Push 하고 난 후의 저장소_

매우 간단한 상황의 예제를 살펴보았다. 토픽 브랜치에서 수정하고 로컬의 `master` 브랜치에 Merge 한다. 작업한 내용을 공유 저장소에 Push 하고자 할 때는 우선 `origin/master` 브랜치를 Fetch 하고 Merge 해야 한다. 그리고 나서 Merge 한 결과를 다시 서버로 Push 한다. 이런 워크플로가 일반적이며 아래와 같이 나타낼 수 있다.

![소규모 팀에서 사용하는 워크플로](/assets/img/2022-01-13/Git/Contributing to a Project/소규모 팀에서 사용하는 워크플로.png)
_소규모 팀에서 사용하는 워크플로_

## 비공개 대규모 팀

---

이제 비공개 대규모 팀에서의 워크플로를 알아본다. 이런 상황에서는 보통 팀을 여러 개로 나눈다. 각각의 작은 팀이 서로 어떻게 Merge 하는지를 살펴보자.

John과 Jessica는 `featureA` 기능을 함께 작업하게 됐다. Jessica는 Josie와 함께 `featureB` 기능도 작업하고 있는 상황이다. 이런 상황이라면 회사는 Integration-Manager 워크플로를 선택하는 게 좋다. 작은 팀이 수행한 결과물은 Integration-Manager가 Merge 하고 공유 저장소의 `master` 브랜치를 업데이트한다. 팀 마다 브랜치를 하나씩 만들고 Integration-Manager는 그 브랜치를 Pull 해서 Merge 한다.

두 팀에 모두 속한 Jessica의 작업 순서를 살펴보자. 우선 Jessica는 저장소를 Clone 하고 `featureA` 작업을 먼저 한다. `featureA` 브랜치를 만들고 수정하고 커밋한다.

```bash
# Jessica 컴퓨터
$ git switch -c featureA
Switched to a new branch 'featureA'
$ vim lib/simplegit.rb
$ git commit -am 'add limit to log function'
[featureA 3300904] add limit to log function
 1 files changed, 1 insertions(+), 1 deletions(-)
```

수정한 내용을 John과 공유하기 위해 `featureA` 브랜치를 서버로 Push 한다. Integration-Manager만 `master` 브랜치를 업데이트할 수 있기 때문에 `master` 브랜치로 Push를 할 수 없고 다른 브랜치로 John과 공유한다.

```bash
$ git push -u origin featureA
...
To jessica@githost:simplegit.git
 * [new branch]      featureA -> featureA
```

Jessica는 자신이 한 일을 `featureA` 라는 브랜치로 Push 했다는 것을 John에게 알린다. John의 피드백을 기다리는 동안 Jessica는 Josie와 함께 작업하기 위해 서버의 `master` 브랜치를 기반으로 새로운 브랜치를 하나 만든다.

```bash
$ git fetch origin
$ git switch -c featureB origin/master
Switched to a new branch 'featureB'
```

몇 가지 작업을 하고 `featureB` 브랜치에 커밋한다.

```bash
$ vim lib/simplegit.rb
$ git commit -am 'made the ls-tree function recursive'
[featureB e5b0fdc] made the ls-tree function recursive
 1 files changed, 1 insertions(+), 1 deletions(-)
$ vim lib/simplegit.rb
$ git commit -am 'add ls-files'
[featureB 8512791] add ls-files
 1 files changed, 5 insertions(+), 0 deletions(-)
```

그럼 Jessica의 저장소는 아래와 같다.

![Jessica의 저장소](/assets/img/2022-01-13/Git/Contributing to a Project/Jessica의 저장소.png)
_Jessica의 저장소_

작업을 마치고 Push 하려고 했는데 Jesie가 이미 `featureB` 작업을 하고 서버에 `featureBee` 브랜치로 Push 했다는 이메일을 받았다. Jessica는 Jesie의 작업을 먼저 Merge 해야만 Push 할 수 있다.

Merge 하기 위해서 우선 Fetch 한다.

```bash
$ git fetch origin
...
From jessica@githost:simplegit
 * [new branch]      featureBee -> origin/featureBee
```

Jessica가 `featureB` 브랜치에서 작업중일 때, Fetch 해 온 브랜치를 `featureB` 브랜치에 Merge 한다.

```bash
$ git merge origin/featureBee
Auto-merging lib/simplegit.rb
Merge made by the 'recursive' strategy.
 lib/simplegit.rb |    4 ++++
 1 files changed, 4 insertions(+), 0 deletions(-)
```

이 시점에서 Jessica는 Merge 한 `featureB` 작업을 서버로 Push 할 때 서버의 `featureB` 브랜치로 Push 하지 않고 이미 Josie가 생성한 `featureBee` 로 작업 내용을 Push 하고자 한다.

```bash
$ git push -u origin featureB:featureBee
...
To jessica@githost:simplegit.git
   fba9af8..cd685d1  featureB -> featureBee
```

이것은 **refspec** 이란 것을 사용하는 것인데 [Refspec](https://git-scm.com/book/ko/v2/ch00/_refspec) 에서 자세하게 설명한다. 명령에서 사용한 `u` 옵션은 `--set-upstream` 옵션의 짧은 표현인데 브랜치를 추적하도록 설정해서 이후 Push 나 Pull 할 때 좀 더 편하게 사용할 수 있다.

John이 몇 가지 작업을 하고 나서 `featureA` 에 Push 했고 확인해 달라는 내용의 이메일을 보내왔다. Jessica는 John의 작업 내용을 확인하기 위해 다시 한 번 Fetch 한다.

```bash
$ git fetch origin
...
From jessica@githost:simplegit
   3300904..aad881d  featureA -> origin/featureA
```

Jessica의 로컬 `featureA` 브랜치와 Fetch 해 온 John의 작업내용이 같은 `featureA` 브랜치 상에서 어떤 것이 업데이트됐는지 `git log` 명령으로 확인한다.

```bash
$ git log featureA..origin/featureA
commit aad881d154acdaeb2b6b18ea0e827ed8a6d671e6
Author: John Smith <jsmith@example.com>
Date:   Fri May 29 19:57:33 2009 -0700

    changed log output to 30 from 25
```

확인을 마치면 로컬의 `featureA` 브랜치로 John의 작업 내용을 다음과 같이 Merge 한다.

```bash
$ git switch featureA
Switched to branch 'featureA'
$ git merge origin/featureA
Updating 3300904..aad881d
Fast forward
 lib/simplegit.rb |   10 +++++++++-
1 files changed, 9 insertions(+), 1 deletions(-)
```

Jessica는 일부 수정하고, `featureA` 브랜치에 커밋하고, 수정한 내용을 다시 서버로 Push 한다.

```bash
$ git commit -am 'small tweak'
[featureA 774b3ed] small tweak
 1 files changed, 1 insertions(+), 1 deletions(-)
$ git push
...
To jessica@githost:simplegit.git
   3300904..774b3ed  featureA -> featureA
```

위와 같은 작업을 마치고 나면 Jessica의 저장소는 아래와 같은 모습이 된다.

![마지막 Push 하고 난 후의 Jessica의 저장소](/assets/img/2022-01-13/Git/Contributing to a Project/마지막 Push 하고 난 후의 Jessica의 저장소.png)
_마지막 Push 하고 난 후의 Jessica의 저장소_

그럼 `featureA` 와 `featureBee` 브랜치가 프로젝트의 메인 브랜치로 Merge 할 준비가 되었다고 Integration-Manager에게 알린다. Integration-Manager가 두 브랜치를 모두 Merge 하고 난 후에 메인 브랜치를 Fetch 하면 아래와 같은 모양이 된다.

![두 브랜치가 메인 브랜치에 Merge 된 후의 저장소](/assets/img/2022-01-13/Git/Contributing to a Project/두 브랜치가 메인 브랜치에 Merge 된 후의 저장소.png)
_두 브랜치가 메인 브랜치에 Merge 된 후의 저장소_

수많은 팀의 작업을 동시에 진행하고 나중에 Merge 하는 기능을 사용하려고 다른 버전 관리 시스템에서 Git으로 바꾸는 조직들이 많아지고 있다. 팀은 자신의 브랜치로 작업하지만, 메인 브랜치에 영향을 끼치지 않는다는 점이 Git의 장점이다. 아래는 이런 워크플로를 나타내고 있다.

![Managed 팀의 워크플로](/assets/img/2022-01-13/Git/Contributing to a Project/Managed 팀의 워크플로.png)
_Managed 팀의 워크플로_

## 공개 프로젝트 Fork

---

비공개 팀을 운영하는 것과 공개 팀을 운영하는 것은 약간 다르다.

공개 팀을 운영할 때는 모든 개발자가 프로젝트의 공유 저장소에 직접적으로 쓰기 권한을 가지지 않는다. 그래서 프로젝트의 관리자는 몇 가지 일을 더 해줘야 한다. Fork를 지원하는 Git 호스팅에서 Fork를 통해 프로젝트에 기여하는 법을 예제를 통해 살펴본다. Git 호스팅 사이트(Github, BitBucket, repo.or.cz, Gitlab 등) 대부분 Fork 기능을 지원하며 프로젝트 관리자는 보통 Fork 하는 것으로 프로젝트를 운영한다. 다른 방식으로 이메일과 Patch를 사용하는 방식도 있는데 뒤이어 살펴본다.

> `rebase -i` 명령을 사용하면 여러 커밋을 하나의 커밋으로 합치거나 프로젝트의 관리자가 수정사항을 쉽게 이해하도록 커밋을 정리할 수 있다. [히스토리 단장하기](https://git-scm.com/book/ko/v2/ch00/_rewriting_history) 에서 대화식으로 Rebase 하는 방법을 살펴본다.
> 

프로젝트의 웹사이트로 가서 Fork 버튼을 누르면 원래 프로젝트 저장소에서 갈라져 나온, 쓰기 권한이 있는 저장소가 하나 만들어진다. 그러면 로컬에서 수정한 커밋을 Fork 한 저장소에 Push 할 수 있게 된다. 그 저장소를 로컬 저장소의 리모트 저장소로 등록하자. 이름은 `myfork`로 하자.

```bash
$ git remote add myfork <remote-url>
```

이제 등록한 리모트 저장소에 Push 한다. 작업하던 내용을 로컬 저장소의 `master` 브랜치에 Merge 한 후 Push 하는 것 보다 리모트 브랜치에 바로 Push 하는 방식이 훨씬 간단하다. 이렇게 하는 이유는 관리자가 토픽 브랜치를 프로젝트에 포함시키고 싶지 않을 때 토픽 브랜치를 Merge 하기 이전 상태로 master 브랜치를 되롤릴 필요가 없기 때문이다. 관리자가 토픽 브랜치를 Merge 하든 Rebase 하든 Cherry-Pick 하든지 간에 결국 다시 관리자의 저장소를 Pull 할 때는 토픽 브랜치의 내용이 들어 있을 것이다 (`cherry-pick` 명령은 [Rebase와 Cherry-Pick 워크플로](https://git-scm.com/book/ko/v2/ch00/_rebase_cherry_pick) 에서 자세히 다룬다). 

다음과 같이 작업 내용을 Push 할 수 있다.

```bash
# 로컬 master에 Merge 하지 않고 바로 리모트 브랜치에 Push
$ git push -u myfork featureA
```

Fork 한 저장소에 Push 하고 나면 프로젝트 관리자에게 이 내용을 알려야 한다. 이것을 **Pull Request** 라고 한다. Git 호스팅 사이트에서 관리자에게 보낼 메시지를 생성하거나 `git request-pull` 명령으로 이메일을 수동으로 만들 수 있다. GitHub의 Pull Request 기능을 통해 메시지를 만들 수도 있다.

`git request-pull <base-branch> <remote-url>` 명령은 아규먼트를 두 개 입력받는다.

- `<base-branch>` : 작업한 토픽 브랜치의 Base 브랜치
- `<remote-url>` : 토픽 브랜치가 위치한 저장소 URL

이 명령은 토픽 브랜치 수정사항을 요약한 내용을 결과로 보여준다. 예를 들어 Jessica가 John에게 Pull 요청을 보내는 상황을 살펴보자. Jessica는 토픽 브랜치에 두 번 커밋을 하고 Fork 한 저장소에 Push 했다. 그리고 아래와 같이 실행한다.

```bash
$ git request-pull origin/master myfork
The following changes since commit 1edee6b1d61823a2de3b09c160d7080b8d1b3a40:
Jessica Smith (1):
        added a new function

are available in the git repository at:

  git://githost/simplegit.git featureA

Jessica Smith (2):
      add limit to log function
      change log output to 30 from 25

 lib/simplegit.rb |   10 +++++++++-
 1 files changed, 9 insertions(+), 1 deletions(-)
```

관리자에게 이 내용을 보낸다. 이 내용에는 토픽 브랜치가 어느 시점에 갈라져 나온 것인지(`commit 1edee6b`), 수정 사항이 어떤지, Pull 하려면 어떤 저장소의 어떤 브랜치에 접근해야 하는지에 대한 내용(`git://githost/simplegit.git`, `featureA`)이 있다.

프로젝트 관리자가 아니라고 해도 보통 `origin/master` 를 추적하는 `master` 브랜치는 가지고 있다. 그래도 토픽 브랜치를 만들고 일을 하면 관리자가 수정 내용을 거부할 때 쉽게 버릴 수 있다. 토픽 브랜치를 만들어서 주제별로 독립적으로 일을 하는 동안에도 주 저장소의 `master` 브랜치는 계속 수정된다. 하지만 주 저장소 브랜치의 최근 커밋 이후로 Rebase 하면 깨끗하게 Merge 할 수 있다.

그리고 다른 주제의 일을 하려고 할 때는 앞서 Push 한 토픽 브랜치에서 시작하지 말고 주 저장소의 `master` 브랜치로부터 만들어야 한다. 다음과 같다.

```bash
$ git switch -c featureB origin/master
  ... 작업 ...
$ git commit
$ git push myfork featureB
$ git request-pull origin/master myfork
  ... Pull Request를 관리자에게 전달 ...
  ... 관리자가 Pull Request 승인 후 Merge ...
$ git fetch origin
```

각 토픽 브랜치는 일종의 실험실이라고 할 수 있다. 각 토픽은 서로 방해하지 않고 독립적으로 수정하고 Rebase 할 수 있다.

![featureB 수정 작업이 끝난 직후 저장소의 모습](/assets/img/2022-01-13/Git/Contributing to a Project/featureB 수정 작업이 끝난 직후 저장소의 모습.png)
_featureB 수정 작업이 끝난 직후 저장소의 모습_

프로젝트 관리자가 사람들의 수정 사항을 Merge 하고 나서 Jessica의 브랜치를 Merge 하려고 할 때 충돌이 날 수도 있다. 그러면 Jessica가 자신의 브랜치를 origin/master에 Rebase 해서 충돌을 해결하고 다시 Pull Request를 보낸다.

```bash
$ git switch featureA
$ git rebase origin/master
$ git push -f myfork featureA
```

위 명령들을 실행하고 나면 히스토리는 다음과 같다.

![FeatureA에 대한 Rebase가 적용된 후의 모습](/assets/img/2022-01-13/Git/Contributing to a Project/FeatureA에 대한 Rebase가 적용된 후의 모습.png)
_FeatureA에 대한 Rebase가 적용된 후의 모습_

브랜치를 Rebase 해 버렸기 때문에 Push 할 때 `-f` 옵션을 주고 강제로 기존 서버에 있던 `featureA` 브랜치의 내용을 덮어 써야 한다. 아니면 새로운 브랜치를(예를 들어 `featureAv2`) 서버에 Push 해도 된다.

또 다른 시나리오를 하나 더 살펴보자. 프로젝트 관리자는 `featureB` 브랜치의 내용은 좋지만, 상세 구현은 다르게 하고 싶다. 관리자는 `featureB` 담당자에게 상세 구현을 다르게 해달라고 요청한다. `featureB` 담당자는 하는 김에 `featureB` 브랜치를 프로젝트의 최신 master 브랜치 기반으로 옮긴다. 먼저 `origin/master` 브랜치에서 `featureBv2` 브랜치를 새로 하나 만들고, `featureB` 의 커밋들을 모두 Squash 해서 Merge 하고, 만약 충돌이 발생하면 상세 구현을 수정하고 새 브랜치를 Push 한다.

```bash
$ git switch -c featureBv2 origin/master
$ git merge --squash featureB
  ... 상세 구현 수정 작업 ...
$ git commit
$ git push myfork featureBv2
```

`--squash` 옵션은 현재 브랜치에 Merge 할 때 해당 브랜치의 커밋을 모두 커밋 하나로 합쳐서 Merge 한다. 이 때 Merge 커밋은 만들지 않는다. 다른 브랜치에서 수정한 사항을 전부 가져오는 것은 똑같다. 하지만 새로 만들어지는 커밋은 부모가 하나이고 커밋을 기록하기 전에 좀 더 수정할 기회도 있다. 수정한 사항을 전부 가져오면서 그 전에 추가적으로 수정할 내용이 있으면 수정하고 Merge 할 수 있다. 게다가 새로 만들어지는 커밋은 부모가 하나다. `--no-commit` 옵션을 추가하면 커밋을 합쳐 놓고 자동으로 커밋하지 않는다.

수정을 마치면 관리자에게 `featureBv2` 브랜치를 확인해 보라고 메시지를 보낸다.

![featureBv2 브랜치를 커밋한 이후 저장소 모습](/assets/img/2022-01-13/Git/Contributing to a Project/featureBv2 브랜치를 커밋한 이후 저장소 모습.png)
_featureBv2 브랜치를 커밋한 이후 저장소 모습_

## 대규모 공개 프로젝트와 이메일을 통한 관리

---

대규모 프로젝트는 보통 수정사항이나 Patch를 수용하는 자신만의 규칙을 마련해놓고 있다. 프로젝트마다 규칙은 서로 다를 수 있으므로 각 프로젝트의 규칙을 미리 알아둘 필요가 있다. 오래된 대규모 프로젝트는 대부분 메일링리스트를 통해서 Patch를 받아들이는데 예제를 통해 살펴본다.

토픽 브랜치를 만들어 수정하는 작업은 앞서 살펴본 바와 거의 비슷하지만, Patch를 제출하는 방식이 다르다. 프로젝트를 Fork 하여 Push 하는 것이 아니라 커밋 내용을 메일로 만들어 개발자 메일링리스트에 제출한다.

```bash
$ git checkout -b topicA
  ... work ...
$ git commit
  ... work ...
$ git commit
```

커밋을 두 번 하고 메일링리스트에 보내 보자. `git format-patch` 명령으로 메일링리스트에 보낼 mbox 형식의 파일을 생성한다. 각 커밋은 하나씩 이메일 메시지로 생성되는데 커밋 메시지의 첫 번째 라인이 제목이 되고 Merge 메시지 내용과 Patch 자체가 메일 메시지의 본문이 된다. 이 방식은 수신한 이메일에 들어 있는 Patch를 바로 적용할 수 있어서 좋다. 메일 속에는 커밋의 모든 내용이 포함된다.

```bash
$ git format-patch -M origin/master
0001-add-limit-to-log-function.patch
0002-changed-log-output-to-30-from-25.patch
```

`format-patch` 명령을 실행하면 생성한 파일 이름을 보여준다. `-M` 옵션은 이름이 변경된 파일이 있는지 살펴보라는 옵션이다. 각 파일의 내용은 아래와 같다.

```bash
$ cat 0001-add-limit-to-log-function.patch
From 330090432754092d704da8e76ca5c05c198e71a8 Mon Sep 17 00:00:00 2001
From: Jessica Smith <jessica@example.com>
Date: Sun, 6 Apr 2008 10:17:23 -0700
Subject: [PATCH 1/2] add limit to log function

Limit log functionality to the first 20

---
 lib/simplegit.rb |    2 +-
 1 files changed, 1 insertions(+), 1 deletions(-)

diff --git a/lib/simplegit.rb b/lib/simplegit.rb
index 76f47bc..f9815f1 100644
--- a/lib/simplegit.rb
+++ b/lib/simplegit.rb
@@ -14,7 +14,7 @@ class SimpleGit
   end

   def log(treeish = 'master')
-    command("git log #{treeish}")
+    command("git log -n 20 #{treeish}")
   end

   def ls_tree(treeish = 'master')
--
2.1.0
```

메일링리스트에 이메일을 보내기 전에 각 Patch 파일을 손으로 고칠 수 있다. `---` 라인과 Patch가 시작되는 라인(`diff --git` 로 시작하는 라인) 사이에 내용을 추가하면 다른 개발자는 읽을 수 있지만, 나중에 Patch에 적용되지는 않는다.

특정 메일 프로그램을 사용하거나 이메일을 보내는 명령어로 메일링리스트에 보낼 수 있다. 붙여 넣기로 위의 내용이 그대로 들어가지 않는 메일 프로그램도 있다. 사용자 편의를 위해 공백이나 라인 바꿈 문자 등을 넣어 주는 메일 프로그램은 원본 그대로 들어가지 않는다. 다행히 Git에는 Patch 메일을 그대로 보낼 수 있는 도구가 있다. IMAP 프로토콜로 보낸다. 저자가 사용하는 방법으로 Gmail을 사용하여 Patch 메일을 전송하는 방법을 살펴보자. 추가로 Git 프로젝트의 `Documentation/SubmittingPatches` 문서의 마지막 부분을 살펴보면 다양한 메일 프로그램으로 메일을 보내는 방법을 설명한다.

메일을 보내려면 먼저 `~/.gitconfig` 파일에서 이메일 부분 설정한다. `git config` 명령으로 추가할 수도 있고 직접 파일을 열어서 추가할 수도 있다. 아무튼, 아래와 같이 설정을 한다.

```bash
[imap]
  folder = "[Gmail]/Drafts"
  host = imaps://imap.gmail.com
  user = user@gmail.com
  pass = YX]8g76G_2^sFbd
  port = 993
  sslverify = false
```

IMAP 서버가 SSL을 사용하지 않으면 마지막 두 라인은 필요 없고 host에서 `imaps://` 대신 `imap://` 로 한다. 이렇게 설정하면 `git imap-send` 명령으로 Patch 파일을 IMAP 서버의 Draft 폴더에 이메일로 보낼 수 있다.

```bash
$ cat *.patch |git imap-send
Resolving imap.gmail.com... ok
Connecting to [74.125.142.109]:993... ok
Logging in...
sending 2 messages
100% (2/2) done
```

이후 Gmail의 Draft 폴더로 가서 To 부분을 메일링리스트의 주소로 변경하고 CC 부분에 해당 메일을 참고해야 하는 관리자나 개발자의 메일 주소를 적고 실제로 전송한다.

SMTP 서버를 이용해서 Patch를 보낼 수도 있다. 먼저 SMTP 서버를 설정해야 한다. `git config` 명령으로 하나씩 설정할 수도 있지만 아래와 같이 `~/.gitconfig` 파일의 sendemail 섹션을 손으로 직접 고쳐도 된다.

```bash
[sendemail]
  smtpencryption = tls
  smtpserver = smtp.gmail.com
  smtpuser = user@gmail.com
  smtpserverport = 587
```

이렇게 설정하면 `git send-email` 명령으로 패치를 보낼 수 있다.

```bash
$ git send-email *.patch
0001-added-limit-to-log-function.patch
0002-changed-log-output-to-30-from-25.patch
Who should the emails appear to be from? [Jessica Smith <jessica@example.com>]
Emails will be sent from: Jessica Smith <jessica@example.com>
Who should the emails be sent to? jessica@example.com
Message-ID to be used as In-Reply-To for the first email? y
```

명령을 실행하면 아래와 같이 서버로 Patch를 보내는 내용이 화면에 나타난다.

```bash
(mbox) Adding cc: Jessica Smith <jessica@example.com> from
  \line 'From: Jessica Smith <jessica@example.com>'
OK. Log says:
Sendmail: /usr/sbin/sendmail -i jessica@example.com
From: Jessica Smith <jessica@example.com>
To: jessica@example.com
Subject: [PATCH 1/2] added limit to log function
Date: Sat, 30 May 2009 13:29:15 -0700
Message-Id: <1243715356-61726-1-git-send-email-jessica@example.com>
X-Mailer: git-send-email 1.6.2.rc1.20.g8c5b.dirty
In-Reply-To: <y>
References: <y>

Result: OK
```