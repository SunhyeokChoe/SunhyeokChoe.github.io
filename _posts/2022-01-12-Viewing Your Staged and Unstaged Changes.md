---
title: '[Git] Staged와 Unstaged 상태의 변경 내용 보기'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 17:43:00 +0900
categories: [Git, Getting Started]
tags: [Git Staged/Unstaged, Commit History, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

단순히 파일이 변경됐다는 사실이 아니라 어떤 내용이 변경됐는지 살펴보려면 `git status` 명령이 아니라 `git diff` 명령을 사용해야 한다. 보통 우리는 “수정했지만 아직 Staged 파일이 아닌 것”과 “어떤 파일이 Staged 상태인지”가 궁금하기 때문에 `git status` 명령으로도 충분했다. 하지만 파일의 내용이 어떻게 변경됐는지는 알 수 없기 때문에 `git diff` 명령을 사용하는데, Patch처럼 어떤 라인을 추가했고 삭제했는지가 궁금할 때 사용한다. `git diff`는 나중에 더 자세하게 다룬다.

 

README 파일 수정 후 `git add` 명령을 통해 Staged 상태로 만들고 CONTRIBUTING.md 파일은 수정만 해둔다. 이 상태에서 `git status` 명령을 실행하면 아래와 같은 메시지를 볼 수 있다.

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

`git diff` 명령을 실행하면 수정했지만 아직 staged 상태가 아닌 파일을 비교해 볼 수 있다.

```bash
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.

 If you are starting to work on a particular area, feel free to submit a PR
 that highlights your work in progress (and note in the PR title that it's
```

이 명령은 **워킹 디렉터리에 있는 것과 Staging Area에 있는 것을 비교**한다. 그래서 수정하고 아직 Stage하지 않은 것을 보여준다.

만약 커밋하려고 Staging Area에 넣은 파일의 변경 부분을 보고 싶으면 `git diff --staged` 옵션을 사용한다. 이 명령은 저장소에 커밋한 것과 Staging Area에 있는 것을 비교한다.

```bash
$ git diff --staged
diff --git a/README b/README
new file mode 100644
index 0000000..03902a1
--- /dev/null
+++ b/README
@@ -0,0 +1,4 @@
+My Project
```

꼭 잊지 말아야 할 것이 있는데, `git diff` **명령은 마지막으로 커밋한 후에 수정한 것들 전부를 보여주지 않는다. git diff는 Unstaged 상태인 것들만 보여준다.** 이 부분이 조금 헷갈릴 수 있다. 수정한 파일을 모두 Staging Area에 넣었다면 `git diff` 명령은 아무것도 출력하지 않는다. Staged된 파일의 수정내용을 확인하려면 `--staged` 옵션을 사용하자.

CONTRIBUTING.md 파일을 Stage한 후에 다시 수정해도 git diff 명령을 사용할 수 있다. 이때는 Staged 상태인 것과 Unstaged 상태인 것을 비교한다.

```bash
$ git add CONTRIBUTING.md
$ echo '# test line' >> CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file: CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified: CONTRIBUTING.md

$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -119,3 +119,4 @@ at the
 ## Starter Projects

 See our projects list at ....................
+# test line
```

Staged 상태인 파일은 `git diff --cached` 옵션으로 확인한다. `--staged`와 `--cached`는 같은 옵션이다.

```bash
$ git diff --cached
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.

 If you are starting to work on a particular area, feel free to submit a PR
 that highlights your work in progress (and note in the PR title that it's
```

앞으로 계속 git diff 명령을 여기저기서 사용할 것이다. 즐겨쓰거나 결과를 보기 좋게 꾸며주는 Diff 도구가 있다면 그걸 사용할 수도 있다. `git diff` 대신 `git difftool` 명령을 사용해 emerge, vimdiff 같은 도구로 비교할 수 있다. 상용 제품도 사용할 수 있다. `git difftool --tool--help`라는 명령은 사용 가능한 도구를 보여준다.