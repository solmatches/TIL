# 리액트는 실제로 어떻게 동작하는 걸까?

<br/>

> Philip Rabianek의 How Does React Actually Work? 동영상을 보고 정리한 내용이다.

<br/>

리액트는 실제로 어떻게 동작하는걸까?

리액트는 JSX를 이용해 리액트 컴포넌트를 만들어 웹에서 그린다는 것을 알고있다.<br/>
또한 `state`, `component lifecycle`과 같은 놀라운 기능을 사용할 수 있다는 것도. 모든것이 매우 빠르고 유연하다. 그런데 정확히 뒤에서는 어떤 일이 일어나는걸까?

<br/>

## React Element

<br/>

1. 우리는 javascript와 `JSX`를 이용해서 `React element`를 `return` 한다.
2. 하지만 리액트의 반환 값을 확인해보면 `object`인 것을 확인할 수 있다.<br/> 리액트 컴포넌트를 `console`에 찍어보면 확인이 가능하다.

`JSX`는 `React.createElement` 함수를 호출해 element를 변환한다. 우리가 JSX를 사용할 때 `React`를 import 해야하는 이유다.

그럼 변환된 객체를 살펴보자.

- `key`, `ref`, `props`, `type`으로 구성되어있다.
- `props`에는 `children`요소가 있고 이 `children element`들은 또 각각 `key`, `ref`, `props`, `type`을 가지고 있다.
- `type`에는 해당 엘리먼트가 변환될 HTML의 이름이 들어있고 
- `ref`, `key` 는 특별한 속성이다. (추후 알게된다)

> 영상 내용에는 없는 개별 첨부.
> `ref`의 경우 사용자가 `dom`에 직접 접근할 때 사용하는 속성이다. `dom`직접 접근은 react에서 자제를 권하고 있지만, 꼭 필요한 경우에는 사용한다.
> `key`는 아래 내용에 나오지만 자식 요소 리스트가 있을 때 자식 요소들의 변경을 빠르게 알아차리고 필요한 부분만 재렌더하기 위해서 할당하는 고유한 값이다.

<br/>

## React Component

리액트 컴포넌트란 무엇일까?
리액트 컴포넌트는 `element tree` 를 출력하는 `class` 또는 `function` 이다.

출력(output)은 함수형 컴포넌트라면 함수의 반환 값이고 클래스형 컴포넌트라면 `render` 메소드의 반환 값이다. 
출력 뿐아니라 `props`를 이용한 입력(input)도 있다.

여기까지 모든게 아주 명백하고 쉬워 보이겠지만, 이게 매우 까다로운 부분이다.

우리는 실제 코드를 작성할 때 `render 메소드`를 호출하지 않았다. `JSX`를 추가했을 뿐이다.

```jsx
const App = () => {
	return (
		<div>
			App component
		</div>
	)
} 

console.log(App()) 
// {$$type: Symbol(react.element), key: null, 
//props: {children: "App component"}, ref: null, type: "div"}

console.log(<App/>) 
// {$$type: Symbol(react.element), key: null, 
//props: {children: "App component"}, ref: null, type: ()=> {...}}
```

리액트 컴포넌트를 호출하지 않고 콘솔로 찍어보면 React element가 뭔가 이상하다는 것을 확인할 수 있다. `type` 부분이 함수로 되어있을 것이다.

`JSX`가 컴포넌트를 렌더할 때 실제로 React가 뒷편(보이지 않는 곳)에서 호출한다.

함수 컴포넌트면 리액트는 할당된 props를 사용하여 직접 호출할 것이고
클래스 컴포넌트면 리액트는 새 인스턴스를 만들고 render method를 호출할 것이다.

React element는 `DOM node` 를 생성할 수 있을 뿐만 아니라, `component instance`도 생성할 수 있다. 
그럼 `component instance`란 무엇일까?

<br/>

## Component Instance

React element가 컴포넌트를 생성할 때, React는 컴포넌트를 추적해서 컴포넌트의 인스턴스를 만든다. 우리가 익숙히 알고 있듯이 각각의 인스턴스는 `state`와 `lifecycle` 를 가지고 있다.

함수형 컴포넌트는 `React Hooks`를 사용하여 state와 lifecycle에 접근할 수 있고, 클래스 컴포넌트는 미리 정의된 메소드와 개별 인스턴스를 나타내는 `this` 키워드를 이용할 수 있다.

React가 함수를 호출하는 것과 같은 방식으로 인스턴스의 `mount`와 `unmount` 관리도 한다.

<br/>

## Reconciliation (재조정)

리액트는 element tree를 만든다. 이 과정은 매우 빠르다. 왜냐면 React elements는 단지 plain javascript 객체이고 기본적으로 수천개의 인스턴스(개체)를 순식간에 만들 수 있다.

이 과정은 우리가 `render()` 메소드를 호출할 때 발생한다. 

React는 맨위에서 시작해서 맨 아래로 재귀적으로 이동하며 element tree를 생성한다. 컴포넌트를 만나면 컴포넌트를 호출하고 반환된 React element 까지 계속 내려간다.

React의 이 `element tree`는 `메모리`에 유지되고, 이것으로 `가상돔`을 호출한다.

최초 렌더할 때 React는 `전체 트리`를 `DOM에 삽입`한다. 이 과정은 DOM에 매우 큰 부담을 주지만 초기 렌더에서 이건 어쩔 수 없는 부분이다. 그런데 만약 트리를 변경한다면? 각각 다른 return 값과 element로 바뀌어야하는 상태 변경이 생긴다면 어떻게 될까?

React는 아주 빠르게 새 element tree를 생성한다. 이제 tree 두개가 있다. 새 tree를 포함하는 가상돔과 실제 돔을 동기화 시켜야한다. 아까 말했듯이 전체 트리를 다시 렌더링하는 것은 매우 비효율 적이기 때문에 DOM을 바꾸는 것은 까다롭다. 그래서 React는 가장 적게 트리를 변경할 수 있는 방법을 수행하는데 그것은 `Diffing 이라는 알고리즘`으로 `tree의 변경사항을 구별`한다.

사실 이것은 매우 어려운 작업일 것이다. 예를들어, 천개의 요소가 있는 경우 최대 10억번의 작업이 필요해질 수 있다. 현실적이지 않다. 대신 React는 element의 수보다 적은 수의 작업을 한다. 천개의 요소를 천번보다 적은 수의 작업으로 매우 빠르게 수행한다. 이를 위해서는 `두가지 조건`이 있다.

1. 두가지 다른 타입의 엘리먼트가 `완전히 다른 두 element tree`를 생성해야 한다.
2. 변경할 자식 요소 목록에 각각 `고유한 key prop`를 올바른 방식으로 제공해야한다.

```jsx
{ items.map((item, index) => (
	// BAD. index는 고유한 값이 될 수 없다. 예기치 않은 동작을 할 것.
	<div key={index}>{item.name}</div>
	)
}

{ items.map((item, index) => (
	// GOOD. id와 같이 고유한 값을 주어야한다.
	<div key={item.id}>{item.name}</div>
	)
}
```

아까말한 React element 속성 중에 `key` 가 바로 이것이다. 이것은 React diffing 알고리즘에서 매우 중요한 `핵심` 부분이다.

```jsx
<div><Component1/></div> -> <div><Component2/></div>
// 위와같은 element 변경사항에서 리액트는 'unmount'되며 모든 구성요소를 새로 그린다. 

<div                                           
	className="first"          
	somProps="first"        
/>                       
<div                                           
	className="second"          
	somProps="second"        
/>   
// 속성이 다를 때는 어떨까?
// instance는 동일하게 유지되고 속성만 업데이트 된다
```

```jsx
// 자식 리스트가 변경되면 어떻게 될까?
<ul>
	<li>John</li>
	<li>Martin</li>
</ul>

<ul>
	<li>John</li>
	<li>Martin</li>
	<li>Thomas</li> // <- 추가되었다!
</ul>
// 첫번째로 같은 위의 항목이 동일함을 인식하고 세번째 항목이 새 항목인 것을 알아차린다.

// 그러면 목록의 첫번 째 부분에 새 항목을 넣는다면?
<ul>
	<li>Thomas</li> // <- 추가되었다!
	<li>John</li>
	<li>Martin</li>
</ul>
// 이 경우 같은 위치의 항목을 비교하고 모두 다르다는것을 알아차리고 새 트리를 생성한다.
// 하지만 전체 트리를 새로 그리는 것을 막고 싶다면 처음부터 key 값을 주면된다!

<ul>
	<li key="Thomas">Thomas</li> // <- 추가되었다!
	<li key="John">John</li>
	<li key="Martin">Martin</li>
</ul>
// key를 이용해 React에게 이전과 같은 element와 새 element를 구분할 수 있게한다.
// 다시 한번, 이것이 index를 사용하면 안되는 이유이다.
// index를 이용할 경우 첫번째 부터 key값이 변경되니 전체 트리를 새로 그릴것이 뻔하다.
```
<br/>

## Rendering

React의 렌더는 렌더러라는 패키지를 호출하여 제어한다. (React DOM, React Native, etc.)

**React 자체는 컴포넌트와 요소 그리고 diffing part를 정의할 수 있는 라이브러리일 뿐이다. 렌더링을 처리하지 않는다. 표현수단 자체를 제공할 뿐이다.**

렌더링은 렌더러가 처리하는데, 재조정 과정을 시작시 element tree를 생성하고 삽입되어야 할 모든 위치에 삽입해준다.

이상하게 보일 수도 있는데, 웹 개발 측면에서 우리는 `ReactDOM을` 한번만 가져와서 렌더한다. 그런데 어떻게 렌더러가 대부분의 구현을 할 수 있는걸까? 이 질문에 대한 키는 React는 렌더러와 `소통`한다는 것을 알면된다.

방법은 여러가지가 있는데 그 중 `setState` 함수를 살펴보자.

`setState는` 어떻게 동작할까?

```jsx
// React DOM 내부
const instance = new Component();
instance.props = props;
instance.updater = ReactDOMUpdater;
```

`setState`는 클래스 컴포넌트의 일부이며 모든 렌더러는 생성된 클래스에 특수 필드를 설정한다.
이 특수필드는 `updater`라고 부른다.

우리가 React를 웹에서 사용할 때, 클래스의 인스턴스를 생성한 직 후에 ReactDOM이 이 필드를 설정한다. 위의 `React DOM 내부`와 비슷할 것이다.

```jsx
setState(partialState, callback) {
	this.updater.enqueueSetState(this, partialState, callback)
}
```

그런 다음 우리가 `setState를` 호출할 때마다 위와 비슷하게 `updater` 함수가 호출될 것이다.

이제 렌더러와 리액트가 어떻게 소통하는지 알았을 것이다. 

<br/>

> 영상 내용은 아니지만 추가.
> **`React`가 리렌더링되는 조건**
>
> 1. `state` 변경이 있을 때.
> `state`는 `setState`를 통해서만 값을 업데이트 할 수 있다. 위에서 언급했듯 `setState`는 ReactDOM의 `updater`와 연결되어있어서 렌더링이 일어나야함을 감지할 것이다.
> 2. `props` 값이 업데이트 됐을 때.
> 3. `부모 컴포넌트`가 렌더링 될 때
> props의 변경이 없더라도 부모 컴포넌트가 리렌더링되면 같이 리렌더링된다. 
> 위에서 설명했듯이 element tree를 생성할 때 위에서 부터 아래로 진행되기 때문일까? 부모 컴포넌트를 포함한 하위컴포넌트를 하나의 조각으로 보고 diffing 하여 새로 그리는 것으로 그림이 그려지지만 추측일 뿐이다 🤔
>
> 4. `shouldComponentUpdate`에서 true가 반환될 때
> 클래스 컴포넌트에는 `shouldComponentUpdate` 메소드를 가지고 있는데, 이는 state가 변경되거나 새로운 `props`를 전달 받는 경우 실행된다.
> 기본적으로 return 값은 true라서 리렌더링이 되지만 false로 지정하면 리렌더링을 막아줄 수 있다.
> 5. `forceUpdate`가 실행될 때
> `props`나 `state`의 변경이 없더라도 리렌더링을 하고싶을 때 사용할 수 있는 메서드이다. 
>


<br/>

`React Hooks`는 어떻게 렌더링 될까?

React Hook는 기본적으로 위와 같다. 그러나 `updater` 필드 대신 `dispatcher` 객체를 이용한다. 

```jsx
// 내부적으로 이와 비슷할 것이다.
const React = {
//...
	__currentDispatcher: null,
	useState(initialState) {
		return React.__currentDispatcher.useState(initialSatate);
	}
}
```

우리가 `useState`를 호출할 때 해당 호출은 `currentDispatcher`라고 불리는 것에 전달된다. 그리고 이 `dispatcher`는 다시 한번 `ReactDOM`에 의해 설정된다.

```jsx
// ReactDom 내부
const previousDispatcher = React.__currentDispatcher;
React.__currentDispatcher = ReactDOMDispatcher;
let result;
try {
	result = Component(props);
} finally {
	React.__currentDispatcher = previousDispatcher;
} 
```

우리가 컴포넌트를 렌더할 때마다 ReactDOM은 위와 비슷하게 `dispatcher`를 설정할 것이다.

자, 이것이 React가 렌더러와 소통하는 방식이다.

----

> 영상이 영어로 되어있기 때문에 열심히 번역하면서 보았는데, 컴퓨터로 유튜브를 접속하니 친절하게 한글 자막이 있었다.... 분명 스마트 티비에는 영어랑 네덜란드어 밖에 없었는데........ 영어 공부까지 알차게 했다 🤣
