---
title: '[Git] 커밋 되돌리기'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 18:27:00 +0900
categories: [Git, Getting Started]
tags: [git reset, git restore, git checkout, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

종종 완료한 커밋을 수정해야 할 때가 있다. 너무 일찍 커밋했거나 어떤 파일을 빼먹었을 때 그리고 커밋 메시지를 잘못 적었을 때 그렇다. 기존 커밋에 다시 커밋하려면 `--amend`를 사용하면 된다.

```bash
$ git commit --amend
```

만약 마지막으로 커밋하고 나서 수정한 것이 없다면(커밋하자마자 바로 이 명령을 실행하는 경우) 조금 전에 한 커밋과 모든 것이 같고, 커밋 메시지만 수정한다.

커밋을 했는데 Stage하는 것을 깜빡하고 빠트린 파일이 있으면 아래와 같이 고칠 수도 있다.

```bash
$ git commit -m "initial commit"
$ git add forgotten_file
$ git commit --amend
```

여기서 실행한 명령어 3개는 모두 하나의 커밋으로 기록된다.

### 파일 상태를 Unstage로 변경하기

예를 들어 파일 두 개를 수정하고서 따로따로 커밋하려고 했지만, 실수로 `git add *` 명령을 실행해 버린 경우 두 파일 모두 Staging Area에 들어있게 된다. 이제 둘 중 하나를 어떻게 꺼낼까? 우선 `git status` 명령으로 확인해보자.

```bash
$ git add *
$ git status
```

![Untitled](/assets/img/2022-01-12/Git/Undoing Things/Untitled.png)

“Changes to be committed” 밑에 “git restore --staged <file>...” 메시지가 보인다. 이 명령으로 Unstaged 상태로 변경할 수 있다. CONTRIBUTING.md 파일을 Unstaged 상태로 변경해보자.

※ Git 2.23.0 이전 버전의 경우 “git reset HEAD <file>...” 로 보일 것이다. 이 명령 또한 `git restore --staged`와 같은 기능을 한다.

```bash
$ git restore --staged CONTRIBUTING.md
$ git status
```

![Untitled](/assets/img/2022-01-12/Git/Undoing Things/Untitled 1.png)

보다시피 CONTRIBUTING.md 파일이 정상적으로 Unstaged 상태가 됐다.

> `git reset` 명령을 `--hard` 옵션과 함께 사용하면 워킹 디렉터리 파일까지 수정되기에 조심해야 한다. `--hard` 옵션만 사용하지 않는다면 `git reset` 명령은 Staging Area의 파일만 조작하기 때문에 위험하지 않다.
> 

### Modified 파일 되돌리기

어떻게 해야 CONTRIBUTING.md 파일을 수정하고 나서 다시 되돌릴 수 있을까? 그러니까 최근 커밋된 버전으로(아니면 처음 Clone 했을 때처럼 워킹 디렉터리에 처음 Checkout한 그 내용으로) 되돌리는 방법이 무얼까? `git status` 명령이 친절하게 알려준다. 바로 앞 쪽의 예제에서 Unstaged 부분을 보자.

![Untitled](/assets/img/2022-01-12/Git/Undoing Things/Untitled 2.png)

위 메시지는 수정한 파일을 되돌리는 방법을 꽤 정확하게 알려준다. 그대로 해보자.

※ Git 2.23.0 이전 버전을 사용하는 경우 `git restore <file>...` 이 아닌 `git checkout --  <file>...` 로 나타날 것이다. 둘 다 같은 기능을 한다. 앞으로 파일 원복시 최신 트렌드에 맞도록 되도록이면 `checkout`이 아닌 `restore`를 사용하도록 하자.

```bash
$ git restore CONTRIBUTING.md
```

![Untitled](/assets/img/2022-01-12/Git/Undoing Things/Untitled 3.png)

CONTRIBUTING.md 파일이 정상적으로 수정하기 이전 상태로 돌아가 Staging Area에서 보이지 않는 것을 알 수 있다.

> `git checkout -- <file>...` 혹은 `git restore <file>...` 명령은 꽤  위험한 명령이라는 것을 알아야 한다. **원래 파일로 덮어썼기 때문에 수정한 내용은 전부 사라지기 때문이다**. 수정한 내용이 정말로 마음에 들지 않을 때만 사용하도록 하자.

※ checkout 다음에 나오는 “--”는 Git의 고유 옵션이 아닌 Unix의 일반적인 명령어 이다. 의미는 다음에 나오는 모든 문자를 문자열로 취급하라는 것이다.
예) `git checkout -- -f` : 여기서 -f는 Git의 옵션이 아닌 문자열로 취급하므로 -f 라는 이름을 지닌 파일을 원복하게 된다.
여기서 checkout 명령의 단점이 드러나게 된다. 파일 원복과 브랜치 이동이 하나의 명령에 포함되므로 만약 master라는 이름의 파일을 원복하고 싶을때 `git checkout master`로 입력하게 되면 파일 원복이 아닌 master 브랜치로 이동하기 때문이다.
또, “-”로 시작하는 파일을 원복하고 싶어 `git checkout -someFile` 명령을 입력하면 `-someFile` 이라는 옵션이 checkout 명령어에서 지원하지 않는다는 메시지가 뜰 것이다.
그러므로 “--” 옵션을 통해 강제로 문자열화 시켜주어야 하는 것이다.
따라서 Git은 2.23.0 버전 이후 checkout 명령을 두 가지로 나누어 브랜치 이동시 switch, 파일 원복시 restore를 권장하는 것이다.
> 

만약 변경한 내용을 잠시 보존 후 이전 커밋으로 되돌아가야 하는 경우 stash와 branch를 사용하자.

Git으로 커밋한 모든 것들은 언제나 복구할 수 있다. 삭제한 브랜치에 있었던 것도, `--amend` 옵션으로 다시 커밋한 것도 복구할 수 있다. 하지만 **커밋하지 않고 잃어버린 것은 절대로 되돌릴 수 없다**.