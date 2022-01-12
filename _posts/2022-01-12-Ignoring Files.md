---
title: '[Git] 특정 파일 혹은 폴더 무시하기 (.gitignore)'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 17:37:00 +0900
categories: [Git, Getting Started]
tags: [.gitignore, Glob, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

어떤 파일은 Git이 관리할 필요가 없다. 보통 로그 파일이나 빌드 시스템이 자동으로 생성한 파일이 그렇다. 그런 파일을 무시하려면 .gitignore 파일을 생성하고 그 안에 무시할 파일 패턴을 입력하면 된다. 아래는 .gitignore 파일의 예이다.

```bash
$ cat .gitignore
*.[oa]
*~
```

첫 번째 라인은 확장자가 ".o"나 ".a"인 파일을 Git이 무시하라는 것이고 둘째 라인은 ~로 끝나는 모든 파일을 무시하라는 것이다. ".o"와 ".a"는 각각 빌드 시스템이 만들어내는 오브젝트와 아카이브 파일이고 ~로 끝나는 파일은 Emacs나 vi 같은 텍스트 편집기가 임시로 만들어내는 파일이다. 또 log, tmp, pid 같은 디렉터리나, 자동으로 생성하는 문서 같은 것들도 추가할 수 있다. .gitignore 파일은 보통 처음에 만들어 두는 것이 편리하다. 그래서 Git 저장소에 커밋하고 싶지 않은 파일을 실수로 커밋하는 일을 방지할 수 있다.

### .gitignore 파일에 입력하는 패턴 규칙

- 아무것도 없는 라인이나, `#`으로 시작하는 라인은 무시한다.
- 표준 Glob 패턴을 사용한다.
- `/`로 시작하면 하위 디렉터리에 적용되지 않는다(recursivity).
- 디렉터리는 끝에 슬래시 `/`를 사용하는 것으로 표현한다.
- `!`로 시작하는 패턴의 파일은 무시하지 않는다.

Glob 패턴은 정규표현식을 단순하게 만든 것으로 생각하면 되고 보통 Shell에서 많이 사용한다.

- `*`: 문자가 하나도 없거나 하나 이상을 의미
- `[abc]`: 는 중괄호 안에 있는 문자 중 하나를 의미 (이 경우 a 혹은 b 혹은 c)
- `?`: 문자 하나를 의미
- `[0-9]`: 캐릭터 사이에 있는 문자 하나를 의미
- `*` 두 개: 디렉터리 안의 디렉터리까지 의미
ex) a/**/z 패턴은 a/z, a/b/z, a/b/c/z 와 같이 a와 z 사이의 모든 디렉터리 및 파일을 의미함

아래는 .gitignore 파일의 예이다.

```bash
# 확장자가 .a인 파일 무시
*.a

# 윗 라인에서 확장자가 .a인 파일은 무시하게 했지만 lib.a는 무시하지 않음
!lib.a

# 현재 디렉터리에 있는 TODO 파일은 무시하고 subdir/TODO 처럼
# 하위 디렉터리에 있는 파일은 무시하지 않음
/TODO

# build/ 디렉터리에 있는 모든 파일 무시
build/

# doc/notes.txt 파일은 무시하고 doc/server/arch.txt 파일은 무시하지 않음
doc/*.txt

# doc 디렉터리 아래의 모든 .pdf 파일을 무시
doc/**/*.pdf
```
<br/><br/><br/>
***References***

---

- [*다양한 .gitignore 파일 템플릿*](https://github.com/github/gitignore)