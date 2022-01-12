---
title: '[Git] 변경사항 커밋하기'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 17:55:00 +0900
categories: [Git, Getting Started]
tags: [git commit, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

수정한 내용을 커밋하기 위해 Staging Area에 파일을 정리했다. Unstaged 상태의 파일은 커밋되지 않는다는 것을 기억해야 한다. Git은 파일을 생성하거나 수정하고 나서 `git add` 명령으로 추가하지 않으면 커밋하지 않는다. 그 파일은 여전히 Modified 상태로 남아 있을 것이다. 커밋하기 전에 `git status` 명령으로 모든 것이 Staged 상태인지 확인할 수 있다. 그 후에 `git commit`을 실행하여 커밋한다.

```bash
$ git commit
```

Git 설정에서 지정된 편집기가 실행되고, 아래와 같은 텍스트가 자동으로 포함된다(아래 예제는 Vim 편집기의 화면이다. 이 편집기는 Shell의 $EDITOR 환경변수에 등록된 편집기이고 보통은 Vim이나 Emacs를 사용한다. 또 앞에서 설명했듯이 `git config --global core.editor` 명령으로 어떤 편집기를 사용할지 설정할 수 있다)

편집기는 아래와 같은 내용을 표시한다(아래 예제는 vim 편집기).

```
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Changes to be committed:
#new file: README
#modified: CONTRIBUTING.md
#
~
~
~
".git/COMMIT_EDITMSG" 9L, 283c
```

자동으로 생성되는 커밋 메시지의 첫 라인은 비어 있고 둘째 라인부터 `git status` 명령의 결과가 채워진다. 커밋한 내용을 쉽게 기억할 수 있도록 이 메시지를 포함할 수도 있고, 메시지를 전부 지우고 새로 작성할 수도 있다(정확히 무엇을 수정했는지 보여줄 수 있는데, `git commit -v` 옵션을 추가하면 편집기에 **diff 메시지도 추가된다**). 내용을 저장하고 편집기를 종료하면 Git은 입력된 내용(#으로 시작하는 내용 제외)으로 새 커밋을 하나 완성한다.

메시지를 인라인으로 첨부할 수도 있다. commit 명령을 실행할 때 아래와 같이 -m 옵션을 사용한다.

```bash
$ git commit -m "Story 182: Fix benchmarks for speed"
[master 463dc4f] Story 182: Fix benchmarks for speed
 2 files changed, 2 insertions(+)
 create mode 100644 README
```

이렇게 첫 번째 커밋을 작성해보았다. commit 명령은 몇 가지 정보를 출력하는데 위 예제는 (master) 브랜치에 커밋했고 체크섬은 “463dc4f”라고 알려준다. 그리고 수정한 파일이 몇 개이고 삭제됐거나 추가된 라인이 몇 라인인지 알려준다.

Git은 Staging Area에 속한 스냅샷을 커밋한다는 것을 기억해야 한다. 수정은 했지만, 아직 Staging Area에 넣지 않은 것은 다음에 커밋할 수 있다. 커밋할 때마다 프로젝트의 스냅샷을 기록하기 때문에 나중에 스냅샷끼리 비교하거나 예전 스냅샷으로 되돌릴 수 있다.

### Staging Area 생략하기

Staging Area는 커밋할 파일을 정리한다는 점에서 매우 유용하지만 복잡하기만 하고 필요하지 않을 때도 있다. 아주 쉽게 Staging Area를 생략할 수 있다. `git commit` 명령을 실행할 때 `-a` 옵션을 추가하면 Git은 Tracked 상태의 파일을 자동으로 Staging Area에 넣는다. 그래서 `git add` 명령을 실행하는 수고를 덜 수 있다.

```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified: CONTRIBUTING.md

no changes added to commit (use "git add" and/or "git commit -a")

$ git commit -a -m 'added new benchmarks'
[master 463dc4f] Story 182: Fix benchmarks for speed
 1 files changed, 5 insertions(+), 0 deletions(-)
```

이 예제에서는 커밋하기 전에 `git add` 명령으로 "CONTRIBUTING.md" 파일을 Staging Area에 추가하지 않았다는 점을 눈여겨보자.