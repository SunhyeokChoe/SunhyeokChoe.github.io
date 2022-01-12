---
title: '[Git] 커밋 히스토리 조회하기'
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-01-12 18:08:00 +0900
categories: [Git, Getting Started]
tags: [Commit History, Git, VCS, DVCS, Github]
math: true
mermaid: true
pin: false
---

그 동안 커밋했던 내역을 보고싶은 경우 `git log` 명령을 활용하면 된다.

아래 예제에서는 "simplegit"이라는 매우 단순한 프로젝트를 사용한다. 아래와 같이 이 프로젝트를 Clone 한다.

```bash
$ git clone https://github.com/schacon/simplegit-progit
```

이 프로젝트 디렉터리에서 git log 명령을 실행하면 아래와 같이 출력된다.

![Untitled](/assets/img/2022-01-12/Git/Viewing the Commit History/Untitled.png)

특별한 아규먼트 없이 `git log` 명령을 실행하면 저장소의 커밋 히스토리를 시간 순으로 보여준다. 즉, 가장 최근 커밋이 가장 먼저 나온다. 그리고 이어서 각 커밋의 SHA-1 체크섬, 저자 이름, 저자 이메일, 커밋한 날짜, 커밋 메시지를 보여준다.

원하는 히스토리를 검색할 수 있도록 `git log` 명령은 매우 다양한 옵션을 지원한다. 여기에서는 자주 사용하는 옵션을 설명한다.

여러 옵션 중 `-p`는 굉장히 유용한 옵션이다. `-p`는 각 커밋의 diff 결과를 보여준다.

![Untitled](/assets/img/2022-01-12/Git/Viewing the Commit History/Untitled 1.png)

다른 유용한 옵션으로 `-2`가 있는데 최근 두 개의 결과만 보여주는 옵션이다.

![Untitled](/assets/img/2022-01-12/Git/Viewing the Commit History/Untitled 2.png)

`git log -p -2` 처럼 두 옵션을 합쳐 사용하면 두 개의 커밋 히스토리를 diff를 포함해 보여준다.

각 커밋 히스토리의 통계 정보를 보고싶은 경우 `--stat` 옵션을 활용하면 된다.

![Untitled](/assets/img/2022-01-12/Git/Viewing the Commit History/Untitled 3.png)

이 결과에서 어떤 파일이 수정됐는지, 얼마나 많은 파일이 변경됐는지, 또 얼마나 많은 라인을 추가하거나 삭제했는지 보여준다. 요약 정보는 각 커밋의 가장 마지막 줄에 보여준다.

다른 또 다른 유용한 옵션은 `--pretty` 옵션이다. 이 옵션을 통해 히스토리 내용을 보여줄 때 기본 형식 이외에 여러 가지 중 하나를 선택할 수 있다. 몇 개 선택할 수 있는 옵션의 값이 있는데, 이 중 `oneline` 옵션은 각 커밋을 한 라인으로 보여준다. 이 옵션은 많은 커밋을 한 번에 조회할 때 유용하다.

추가로 `short`, `full`, `fuller` 옵션도 있는데 이것은 정보를 조금씩 가감해서 보여준다.

![Untitled](/assets/img/2022-01-12/Git/Viewing the Commit History/Untitled 4.png)

사용자가 직접 지정한 포맷으로 결과를 출력하고 싶은 경우 `format` 옵션을 활용한다. 특히 조회 결과를 다른 프로그램으로 파싱하고자 할 때 유용하다. 이 옵션을 사용하면 포맷을 정확하게 일치시킬 수 있기 때문에 Git을 새 버전으로 바꿔도 결과 포맷이 바뀌지 않는다.

![Untitled](/assets/img/2022-01-12/Git/Viewing the Commit History/Untitled 5.png)

![git log --pretty=format에 쓸 수 있는 몇 가지 유용한 옵션.png](/assets/img/2022-01-12/Git/Viewing the Commit History/git_log_--prettyformat에_쓸_수_있는_몇_가지_유용한_옵션.png){:width="50%"}
_git log --pretty=format에 쓸 수 있는 몇 가지 유용한 옵션_

저자(author)와 커미터(committer)를 구분하는 것이 조금 이상해 보일 수 있다. **저자는 원래 작업을 수행한 원작자이고 커미터는 마지막으로 이 작업을 적용한(저장소에 포함한) 사람이다.** 만약 여러분이 어떤 프로젝트에 패치를 보냈고 그 프로젝트의 담당자가 패치를 적용했다면 두 명의 정보를 알 필요가 있다. 그래서 이 경우 여러분이 저자고 그 담당자가 커미터다.

`oneline`과 `format` 옵션은 `--graph` 옵션과 함께 사용할 때 더 빛난다. 이 명령은 브랜치와 머지 히스토리를 보여주는 아스키 그래프를 출력한다.

![Untitled](/assets/img/2022-01-12/Git/Viewing the Commit History/Untitled 6.png)

![git log 주요 옵션.png](/assets/img/2022-01-12/Git/Viewing the Commit History/git_log_주요_옵션.png)
_git log 주요 옵션_

## 조회 제한조건

---

`git log` 명령엔 조회 범위를 제한하는 옵션도 있다. 이미 최근 두 개의 커밋만 조회하는 `-2` 옵션은 살펴봤다. 실제 사용법은 `-<n>`이고 n은 최근 n개 커밋을 의미한다.

`--since`나 `--until` 같은 시간을 기준으로 조회하는 옵션 또한 있다.

```bash
$ git log --since=2.weeks
```

이 옵션은 다양한 형식을 지원한다. "2010-5-25"같이 정확한 날짜로 지정할 수도 있고 "2 years 1 day 3 minutes ago"와 같이 상대적인 기간을 사용할 수도 있다.

또 다른 기준도 있다. `--author` 옵션으로 저자를 지정하여 검색할 수도 있고 `--grep` 옵션으로 커밋 메시지에서 키워드를 검색할 수도 있다. author와 grep옵션을 함께 사용하여 조건에 모두 만족하는 커밋을 찾으려면 `--all-match` 옵션도 반드시 함께 사용해야 한다.

유용한 옵션으로는 `-S`가 있는데 이 옵션은 코드에서 추가되거나 제거된 내용 중에 특정 텍스트가 포함되어 있는지를 검색한다. 예를 들어 어떤 함수가 추가되거나 제거된 커밋만을 찾아볼 수 있다.

![Untitled](/assets/img/2022-01-12/Git/Viewing the Commit History/Untitled 7.png)

“0.1.1” 이라는 문자가 해당 커밋에서 추가되었고, 이를 표시한다.

![git log 조회 범위를 제한하는 옵션.png](/assets/img/2022-01-12/Git/Viewing the Commit History/git_log_조회_범위를_제한하는_옵션.png){:width="75%"}
_git log 조회 범위를 제한하는 옵션_
