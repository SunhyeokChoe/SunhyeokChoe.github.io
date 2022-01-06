---
title: 프론트엔드 웹 서비스에서 우아하게 비동기 처리하기
author:
  name: SunhyeokChoe
  link: https://sunhyeokchoe.github.io
date: 2021-10-05 11:50:00 +0800
categories: [React, SLASH21 - 토스개발자컨퍼런스]
tags: [React Suspense, Data Fetching]
math: true
mermaid: true
pin: false
---

API를 호출하거나 네이티브 앱과 통신할 때 프론트엔드 웹 서비스에서는 반드시 비동기 작업이 일어나게 됩니다.
일상처럼 다루고 있지만 정작 UI에서 다루기 힘든 비동기 프로그래밍.
React Suspense를 이용하여 우아하게 처리하는 이론과 실전 적용법을 공유합니다.

## 토스의 프로젝트 구조

---

- 토스 앱 내에서 WebView를 이용해 iOS/Android 공통의 웹 서비스를 개발하고 있음
- 60개 이상의 크고 작은 활성 서비스가 웹 기술을 이용하여 개발되고 있음
- 그 외의 홈페이지나 토스증권, 토스페이먼츠, 토스인슈어런스 등의 서비스는 100% 웹 기술을 이용해서 개발되고 있음
- 토스 앱 안에서는 주식 탭, 혜택 탭 등이 대표적으로 웹 서비스를 이용해서 개발된 서비스임
- 이렇게 많은 서비스들은 마이크로프론트엔드 아키텍처(**모노레포**)를 이용해서 같은 레포지토리 안에서 독립적으로 개발/배포되고 있음
- 모든 서비스들은 React, TypeScript, Next.js 기술 스택을 공유하고 있고, 구체적인 로직만 다르게 가져가고 있음

## 웹 서비스, UI 개발에서 가장 다루기 어려운 부분은?

---

웹에서는 10여년 전 JQuery를 활용해서 명령형으로 프로그래밍을 하다가 React/Vue.js와 같이 선언적인 프로그래밍을 지원하는 프레임워크들이 나오면서 각각의 개발자가 신경써야 하는 부분들이 많이 줄었다.

그럼에도 아직까지 다루기 어려운 영역을 하나 꼽아보자면 **비동기 프로그래밍**을 꼽을 수 있다. 비동기 프로그래밍은 '순서가 보장되지 않는 상황'으로 요약할 수 있다.

비동기 프로그래밍은 끊기지 않는 60프레임의 좋은 사용자 경험을 위해서는 필수이고, JavaScript에서는 Callback, Promise, Observable(using RxJs)과 같이 다양한 도구를 이용해서 비동기적인 상황을 다루고 있다.

그럼에도 불구하고 여전히 다루기가 어렵다.

이유는 뭘까?

## 좋은 코드에 대해 돌아보자

---

이 코드의 문제점은 무엇일까?

```jsx
function getBazFromX(x) {
	if (x === undefined) {
		return undefined;
	}

	if (x.foo === undefined) {
		return undefined;
	}

	if (x.foo.bar === undefined) {
		return undefined;
	}

	return x.foo.bar.baz;
}
```

- 하는 일은 단순하지만 코드가 너무 복잡하다.
- 각 프로퍼티에 접근하는 핵심 기능이 코드로 잘 드러나지 않는다.

이 함수가 하는 일을 요약한다면, x.foo.bar.baz 라고 하는 프로퍼티에 안전하게 접근하는 일인데, 함수가 하는 일이 명확하게 드러난다기 보다는 'x가 없는지 검사한다.', 'x.foo가 없는지 검사한다.', 'x.foo.bar'가 없는지 검사한다'와 같이 명령어의 노이즈가 많아 함수가 어떤 역할을 하는지 명확하게 드러나지 않는다.

이를 해결하기 위해 최근 ECMAScript에 추가된 Optional Chaining 문법을 활용한 동일한 함수를 살펴보자.

```jsx
function getBazFromX(x) {
	return x?.foo?.bar?.baz;
}
```

일전의 비효율적인 함수와 다른점이 무엇일까?

- '성공한 경우'를 생각하는 x.foo.bar.baz와 문법적 차이가 크지 않다.
- 함수의 역할을 한눈에 파악할 수 있다.

우선 함수가 하는 일을 흐리게 만들던 if문들이 사라져 코드가 간결해진 덕분에 어떤 역할을 하는 함수인지 한눈에 확인할 수 있다.

또한, 잘 살펴보면 Nullable이 아닐 때, 즉 성공할 때 접근하는 모습을 나타내는 x.foo.bar.baz라고 하는 표현식과 모양이 큰 차이가 없는 것도 확인할 수 있다.

같은 역할을 하는 식이 비슷하게 표현된다는 것은 코드에 있어서 좋은 징조 중 하나이다.

더 복잡한 예제를 살펴보자.

이 코드의 문제점은 무엇일까?

(JavaScript에 Promise가 없던 시절 비동기를 처리하기 위해 callback을 사용했던 코드)

```jsx
function fetchAccounts(callback) {
	fetchUserEntity((err, user) => {
		if (err != null) {
			callback(err, null);
			return;
		}

		fetch(UserAccounts(user.no, (err, accounts) => {
			if (err != null) {
				callback(err, null);
				return;
			{

			callback(null, accounts);
		});
	});
}
```

fetchUserEntity를 호출해서 그 결과를 Callback으로 받는데, 에러가 있으면 에러를 Emit한다.

그리고 그 결과값을 이용해서 사용자의 계좌 목록을 가져오는데, 마찬가지로 에러가 있으면 에러를 Emit하고, 그렇지 않을 경우 실제값을 Emit 한다.

- '성공하는 경우'와 '실패하는 경우'가 섞여서 처리된다.
- 코드를 작성하는 입장에서 매번 에러 유무를 확인해야 한다.

이 함수를 요약하자면, user를 가져오고, 그 정보를 바탕으로 accounts를 가져오고, 그 값을 반환하는 역할이다. 중간에 '실패하는 경우'에 대한 처리가 섞여 있어서 함수가 하는 진짜 역할이 가려졌다(노이즈).

또한, 코드를 작성하는 입장에서 매번 비동기 호출을 할 때마다 에러 처리를 해줘야 한다는 점이 불편한 점 중 하나라고 할 수 있다.

이를 해결하기 위해 async-await 문법을 활용해보자.

```jsx
async function fetchAccounts() {
	const user = await fetchUserEntity();
	const accounts = await fetchUserAccounts(user.no);
	return accounts;
}
```

왜 이 코드를 좋은 코드라고 말할 수 있을까?

- '성공하는 경우'만 다루고, '실패하는 경우'는 catch 절에서 분리해서 처리한다.
- '실패하는 경우'에 대한 처리를 외부에 위임할 수 있다.

비동기 요청을 통해 '성공하는 경우'들만 모아서 살펴볼 수 있기에 함수가 하는 역할이 명확히 드러난다. 동기적인 코드가 외견적으로는 큰 차이가 없다.

또한, 별도로 에러를 처리하는 부분이 없고 모든 에러 처리는 외부에 위임된다라고 하는 점도 좋은 코드임을 드러내는 부분 중 하나이다.

좋은 코드의 특징

- 성공, 실패의 경우를 분리해 처리할 수 있다.
- 비즈니스 로직을 한눈에 파악할 수 있다.

함수에는 성공하는 경우들만 적혀 있으니 읽기도 쉽고, 함수의 책임이 명확히 드러난다.

어려운 코드의 특징

- 실패, 성공의 경우가 서로 섞여 처리된다.
- 비즈니스 로직을 파악하기 어렵다.

'실패하는 경우'와 '성공하는 경우'가 섞여서 처리된다는 점이 함수의 책임을 알아보기 어렵게 한다. 덕분에 함수의 크기가 커지고, 하는 역할이 명시적으로 드러나지 못한다.

## 프론트엔드 웹 서비스에서 비동기 처리는 지금까지 어땠는가?

---

우리는 보통 API 호출과 같은 상황을 처리할 때 어떻게 처리했을까?

**in React**

API 호출

```jsx
const { data, error } = useAsyncValue(() ⇒ {
	return fetchSomething();
});
```

SWR이나 react-query와 같은 라이브러리를 많이 활용했다.

Promise를 반환하는 함수를 React Hook의 인자로 넘기고, Promise의 상태 변화에 따라 Hook이 반환하는 data, error의 값을 적절히 채워주는 것이다.

그리고 아래와 같이 컴포넌트를 작성하고는 했다.

컴포넌트 처리

```jsx
function Profile() {
	const foo = useAsyncValue(() => {
		return fetchFoo();
	});

	if (foo.error) return <div>로딩에 실패했습니다.</div>
	if (!foo.data) return <div>로딩 중입니다.</div>
	return <div>{foo.data.name}님 안녕하세요!</div>
}
```

이 함수를 살펴보면, 비동기인 foo를 가져오는데, foo가 에러이면 실패 메시지를 보여주고, foo가 없으면 로딩중이라고 보여주고, foo가 있으면 안녕하세요 라고 하는 메시지를 보여주는 식이다.

위 코드는 다음과 같은 문제점이 있다.

- '성공하는 경우'와 '실패하는 경우'가 섞여서 처리된다.
- '실패하는 경우'에 대한 처리를 위부에 위임하기 어렵다.

**이러한 문제는 여러 개의 비동기 작업이 동시에 실행될 때 심각한 일이 발생한다!**

방금 전에 봤던 callback 코드와 비슷하게 코드가 점점 읽기 어려워지는 것이다.

한번 코드를 살펴보자. (foo와 bar 값을 비동기로 가져오는 함수)

```jsx
/* 비동기 코드 지옥 */

function Profile() {
	const foo = useAsyncValue(() => {
		return fetchFoo();
	});

	const bar = useAsyncValue(() => {
		if (foo.error || !foo.data) {
			return undefined;
		}

		return fetchBar(foo.data);
	});

	if (foo.error || bar.error) return <div>로딩에 실패했습니다.</div>
	if (!foo.data || !bar.data) return <div>로딩 중입니다.</div>
	return /* foo와 bar로 적합한 처리하기 */
}
```

bar를 가져오기 위해서는 foo가 있어야 한다.

결국 bar는 foo가 로드될 때까지 기다리고, if문은 복잡해지고... 그냥 복잡하다.

보통은 하나의 비동기 작업은 **(로딩중 / 에러 / 완료됨)** 3가지의 상태를 가지고 있다.

![Three states of async job](/assets/img/2021-10-05/Three states of async job.png)
_Three states of async job_

만약에 2개의 비동기 작업이 있다면, 3^2=9가지의 상태를 가질 수 있다는 것을 생각할 수 있다.

그렇다면 비동기 호출이 3개, 4개가 된다면 더욱 복잡해질 것이다.

**React의 비동기 처리는 어렵다**는 내용을 간략화 하자면

- 성공하는 경우에만 집중해 컴포넌트를 구성하기 어렵다.
- 2개 이상의 비동기 로직이 개입할 때, 비즈니스 로직을 파악하기 점점 어려워진다.

React에서 지금까지 살펴보았던 Hook이나 State를 사용하는 방식으로는 이렇게 간단히 비동기 처리를 할 수가 없다.

**다행히도 이 문제를 우아하게 해결해주는 도구가 있다!**

## React 팀이 제안하는 "React Suspense for Data Fetching"

---

한국어로 번역하자면 "데이터를 가져오기 위한 Suspense"라고 한다. 아직은 React의 experimental, 즉 실험 버전에서만 사용할 수 있다.

React Suspense for Data Fetching이 목표로 하는 코드는 간단하다. 쉽게 말해 async-await 급으로 비동기를 처리하면서 간단하고 읽기 편한 React 컴포넌트를 만들겠다고 하는 것이다. 다시 말해, 컴포넌트는 성공한 상태만 다루고, 로딩 상태와 에러 상태는 외부에 위임함으로써 동기적인 코드와 큰 차이가 없는 코드를 만들겠다는 비전이다.

실제로 아래와 같이 FooBar 컴포넌트의 useAsyncValue를 동기적인 계산을 하는 useMemo로 거의 그대로 치환하면, 완벽히 똑같은 구조를 가지고 있는 것을 확인할 수 있다.

```jsx
/* useAsyncValue 버전 */
function FooBar() {
	const foo = useAsyncValue(() => fetchFoo());
	const bar = useAsyncValue(() => fetchBar());

	return <div>{foo}{bar}</div>
}

/* useMemo 버전 */
function FooBar() {
	const foo = useMemo(() => fetchFoo());
	const bar = useMemo(() => fetchBar());

	return <div>{foo}{bar}</div>
}
```

React Suspense for Data Fetching은 이러한 useAsyncValue와 같은 hook을 만들 수 있는 Low-level API를 제공한다. 

만약에 비동기 작업을 아래와 같이 처리한다면, 로딩 상태나 에러 상태는 어떻게 처리해야 할까?

```html
<ErrorBoundary fallback={<MyErrorPage />}>
	<Suspense fallback={<Loader />}>
		<FooBar />
	</Suspense>
</ErrorBoundary>
```

함수의 에러 처리를 감싸는 catch 문에서 하는 것처럼 로딩 상태와 에러 처리도 컴포넌트를 쓰는 곳에서 처리해주면 된다.

- 컴포넌트를 '쓰는 쪽'에서 로딩 처리와 에러를 처리한다.
- 로딩 상태는 가장 가까운 'Suspense'의 'Fallback'으로 그려진다.
- 에러 상태는 가장 가까운 'ErrorBoundary'가 componentDidCatch()로 처리한다.

방금 작성한 ErrorBoundary/Suspense를 살펴보면 다음과 같은 코드와 거의 유사하다

```jsx
try {
	await fetchFooBar();
} catch (error) {
	// 에러 처리
}
```

비동기 콜을 하는 함수나 컴포넌트가 가운데에 있고, 실패하는 경우를 처리하는 부분이 그 부분을 감싸고 있다.

ex) ErrorBoundary 컴포넌트가 Suspense와 FooBar 컴포넌트를 감싸고 있음

ex) 비동기 함수 fetchFooBar를 call하는 부분을 try...catch로 감싸고 있음

우리가 모든 실패할 수 있는 함수에 try...catch를 감싸지 않는 것처럼, Suspense를 일으키는 모든 컴포넌트에 Suspense나 ErrorBoundary를 붙여주기보다는 적당한 부분 단위로 에러와 로딩 상태를 한 번에 처리하게 된다.

예를 들어 아래 코드는 App 전체에서 로딩 상태와 에러 상태를 처리해주는 핸들러를 선언한 것이다.

```html
<ErrorBoundary fallback={<ErrorPage />}>
	<Suspense fallback={<Loader />}>
		<App />
	</Suspense>
</ErrorBoundary>
```

이렇게 비동기를 동기적으로 바꿔주는 Suspense 기능을 이용하기는 전혀 어렵지 않다.

사용하는 라이브러리에서 Suspense를 사용한다고 선언해주면 된다.

![Suspense 지원 라이브러리](/assets/img/2021-10-05/Suspense 지원 라이브러리.png)
_Suspense 지원 라이브러리_

각 라이브러리에서 위와 같은 옵션을 사용하면 자동으로 컴포넌트의 Suspense 상태가 관리된다.

React Suspense를 사용하면 로딩과 에러 처리를 바깥에 위임하여 비동기 작업을 동기와 똑같이 처리할 수 있었는데, React 팀의 *Sebastian*이 만들었던 코드 스니펫은 이런 '마법'이 어떻게 React에서 구현되어 있는지 보여준다.

```jsx
/* React Team의 *Sebastian markbage*의 Proof-of-concept */
function getUserName(id) {
	var user = JSON.parse(fetchTextSync('/users/' + id);
	return user.name;
}

function getGreeting(name) {
	if (name === 'Seb') {
		return 'Hey';
	}
	return fetchTextSync('/greeting');
}
function getMessage() {
	let name = getUserName(123);
	return getGreeting(name) + ', ' + name + '!';
}
runPureTask(getMessage).then(message => console.log(message));
```

runPureTask로 실행시키면 비동기 함수도 동기적으로 작성할 수 있다.

코드를 살펴보면 fetchTextSync라고 하는 함수는 원래 API 호출으로 비동기 작업이지만, 동기처럼 사용되고 있는 걸 볼 수 있다. 이 모든것을 가능하게 해주는 것은 runPureTask라고 하는 런타임이다.

## 토스팀에서 적용한 제품 예시

---

토스팀에서는 최근에 개발된 서비스 일부를 React Suspense를 활용해서 마이그레이션을 진행하고 있다. 사용 경험이 좋았는데, 대표적으로 적용된 제품은 토스팀의 TUBA라고 하는 제품이다.

![TUBA](/assets/img/2021-10-05/TUBA.png)
_TUBA_

TUBA 제품은 토스 대부분의 데이터가 모이고 분석되는 내부 제품이며, 다양한 A/B 테스트를 설정하거나, 알림이나 푸시를 전송하는 등 다양한 작업을 수행하고 있다.

토스 앱에서 알림이나 푸시를 보낼 때 토스팀이 사용하는 제품이 바로 'TUBA 메신저' 인데, TUBA 메신저의 메시지 상세 화면에서는 상당히 복잡한 비동기 처리가 필요했다.

![직관성을 떨어뜨리는 다양한 비동기 리소스](/assets/img/2021-10-05/직관성을 떨어뜨리는 다양한 비동기 리소스.png)
_직관성을 떨어뜨리는 다양한 비동기 리소스_

API 호출로 가져와야 하는 부분들이 이렇게나 다양했다.

어떤 메시지였는지, 메시지의 내용이 무엇인지, 발송 일정이 뭔지, 통계 정보가 어떻게 나왔는지 등 다양한 데이터를 복잡한 조건 하에 가져와야 하는 니즈가 있었다.

**토스는 이런 문제를 Recoil의 비동기 셀렉터를 이용해 해결할 수 있었다!**

Recoil에서는 비동기 리소스를 다음과 같이 selector 또는 selectorFamilty로 정의할 수 있다.

```jsx
export const templateSetSelector = selectorFamily({
		key: '@messages/template-set',
		get: (no: number) => async () => {
		return fetchTemplateSet(no);
	},
});
```

templateSetSelector는 no라고 하는 번호를 인자로 받아 fetchTemplateSet이라고 하는 비동기 호출을 보내는 것을 볼 수 있다.

이렇게 정의한 비동기 리소스를 useRecoilValue를 이용해서 가져오려고 하면 Suspense가 발생하게 된다. 

```jsx
function TemplateSetDetails({ templateSetNo }: Props) {
	const templateSet = useRecoilValue(templateSetSelector(templateSetNo));
	/* 이 아래에서는 templateSet이 존재하는 것이 타입적으로 완전히 보장됨 */
}
```

```html
<Suspense fallback={<Skeleton />}>
	<TemplateSetDetails />
</Suspense>
```

이렇게 비동기 호출을 하는 컴포넌트를 적절히 Suspense로 감싸주기만 하면 된다.

사용자 경험 측면에서도 데이터가 준비되는 대로 하나씩 자연스럽게 보여줄 수 있어 좋다고 할 수 있다.

## React Hooks와의 유사도

---

React Suspense 덕분에 많은 비동기적인 문제를 깔끔하고 우아하게 처리할 수가 있게 되었고, 코드의 복잡도도 줄일 수 있었다.

2년 반 정도 전에 나왔던 React Hooks는 이제 엄청 익숙하지만, 비슷한 역할을 해주고 있다.

웹 서비스의 코드 복잡도를 줄여주고, 상태, 이펙트와 메모이제이션과 같이 자주 발생하는 작업들을 매우 쉽게 사용할 수 있게 해주었다.

![React Hooks](/assets/img/2021-10-05/React Hooks.png)
_React Hooks_

React Hooks는 어떻게 토스팀 프로젝트 코드의 복잡도를 줄여주었을까?

그 중 **선언적인 API**의 비중이 굉장히 컸다.

이전의 클래스 컴포넌트에서는 컴포넌트의 라이프사이클에 맞춰 다양한 작업을 **명령형**으로 해주어야 했지만, Hooks를 사용하면서 상황이 달라졌다.

useState로 상태를 사용한다고 **선언**하고, useMemo로 메모이제이션을 한다고 **선언**하고, useEffect로 효과를 발생시킨다고 **선언**했다. 이렇게 선언하기만 하면 React 프레임워크가 실제 작업을 대신해주었다.

![Suspense for Data Fetching](/assets/img/2021-10-05/Suspense for Data Fetching.png)
_Suspense for Data Fetching_

Suspense도 비슷하다. 컴포넌트에서는 비동기적인 리소스를 선언하고, 그 값을 읽어온다고 **선언**하기만 한 것이다. 그러면 실제 로딩 상태나 에러 상태처리는 컴포넌트를 감싸는 부모 컴포넌트가 대신해주었다.

또 비슷한 것이 있다. 바로 우리가 자주 사용하는 try...catch 문이다.

![기존 예외처리 방법](/assets/img/2021-10-05/기존 예외처리 방법.png)
_기존 예외처리 방법_

실패할 수 있는 함수는 throw 문으로 에러를 발생시키고, 실제 에러 처리는 컴포넌트를 감싸는 부모 함수가 수행해주는 것이다.

이렇게 어떤 코드 조각을 감싸는 맥락으로 책임을 분리하는 방식을 **대수적 효과(Algebraic Effects)**라고 한다. 객체지향의 의존성 주입(DI), 의존성 역전(IoC)과도 유사하다.

대수적 효과를 지원하는 언어에서 함수는 필요한 코드 조각을 선언적으로 사용한다.

예를 들면, 메모이제이션이 필요하면 useMemo를 호출하는 식이다. 그러면 **실제로 관련된 처리는 함수를 감싸는 부모 함수나 런타임이 대신 처리하는 형식**이 된다.

## 하지 못한 이야기들 (React에서 사용자 경험을 더욱 향상시킬 수 있는 React 요소)

---

- React Concurrent Mode
- useTransition, useDeferredValue

위 요소들을 사용한다면 React에서 컴포넌트 렌더 트리를 부분적으로만 완성함으로써 사용자 경험을 크게 향상시킬 수 있다.

**비동기 작업 뿐만이 아니라 Debounce 등으로 처리하던 무거운 동기적 작업에도 적용할 수 있다.**

<br/>

***Reference***

---

- [*토스ㅣSLASH 21 - 프론트엔드 웹 서비스에서 우아하게 비동기 처리하기*](https://toss.im/slash-21/sessions/3-1)
- [*YouTube*](https://www.youtube.com/watch?v=FvRtoViujGg&t=543s&ab_channel=%ED%86%A0%EC%8A%A4)
- [*데이터를 가져오기 위한 Suspense (실험 단계) (React Suspense for Data Fetching)*](https://ko.reactjs.org/docs/concurrent-mode-suspense.html)
- *Dan Abramov*