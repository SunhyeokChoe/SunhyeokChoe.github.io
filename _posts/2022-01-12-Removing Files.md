---
title: '[Git] 파일 삭제하기'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 17:58:00 +0900
categories: [Git, Getting Started]
tags: [git rm, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

Git에서 파일을 제거하려면 `git rm` 명령으로 Tracked 상태의 파일을 삭제한 후(정확하게는 Staging Area에서 삭제하는 것) 커밋해야 한다. 이 명령은 워킹 디렉터리에 있는 파일도 삭제하기 때문에 실제로 파일도 지워진다. Git 명령을 사용하지 않고 단순히 워킹 디렉터리에서 파일을 삭제하고 `git status` 명령으로 상태를 확인하면 Git은 현재 "Changes not staged for commit" 즉, Unstaged 상태라고 표시해준다.

```bash
$ rm grit.gemspec
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    deleted: grit.gemspec

no changes added to commit (use "git add" and/or "git commit -a")
```

그리고 다시 `git rm` 명령을 실행하면 삭제한 파일은 Staged 상태가 된다.

```bash
$ git rm grit.gemspec
rm 'grit.gemspec'
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    deleted: grit.gemspec
```

커밋하면 파일은 삭제되고 Git은 이 파일을 더는 추적하지 않는다. 이미 파일을 수정했거나, 수정한 파일을 Index(Staging Area)에 추가했다면 `-f` 옵션을 주어 강제로 삭제해야 한다. 이 점은 실수로 데이터를 삭제하지 못하도록 하는 안전장치다. 커밋하지 않고 수정한 데이터는 Git으로 복구할 수 없기 때문이다.

또 Staging Area에서만 제거하고 워킹 디렉터리에 있는 파일은 지우지 않고 남겨둘 수 있다. 다시 말해서 하드디스크에 있는 파일은 그대로 두고 Git만 추적하지 않게 된다. 이것은 .gitignore 파일에 추가하는 것을 빼먹었거나, 대용량 로그 파일이나 컴파일된 파일인 .a 파일 같은 것을 실수로 추가했을 때 아주 유용하다. `--cached` 옵션을 사용하여 명령을 실행한다.

```bash
$ git rm --cached README
```

여러 개의 파일이나 디렉터리를 한꺼번에 삭제할 수도 있다. 아래와 같이 `git rm` 명령에 file-glob 패턴을 사용한다.

```bash
$ git rm log/\*.log
```

`*` 앞에 \를 사용한 것을 기억하자. **파일명 확장 기능은 Shell에만 있는 것이 아니라 Git 자체에도 있으므로 필요하다.** 이 명령은 log/ 디렉터리에 있는 모든 .log 파일을 삭제한다.

아래의 예제처럼 할 수도 있다.

```bash
$ git rm \*~
```

이 명령은 ~로 끝나는 파일을 모두 삭제한다.

※ ~로 끝나는 파일은 Emacs나 vi 같은 텍스트 편집기가 임시로 만들어내는 파일이다.