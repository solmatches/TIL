## function (declaration) vs arrow function

### 일반 함수와 화살표 함수

---

<br/>

> 커스텀 훅이나 util 함수를 작성할 때 일반 함수로 작성되는 이유가 궁금했다. 화살표 함수로 작성되는 경우도 보이기는 하지만 대부분 일반 함수로 작성되는 이유가 뭘까?

우선 일반 함수와 화살표 함수의 차이점을 알아보자.

<br/>

### 0. **형태의 차이**

```javascript
// 일반함수 (선언식 버전)
function a() {
	return "hi";
}

// 일반함수 (표현식 버전)
const a = function () {
	return "hi";
};
// 변수에 익명함수를 할당한다.

// 화살표 함수
const a = () => "hi";
// es6 부터 도입된 함수 표현 형태이다.
// 구문이 짧아 간결한 함수 작성이 가능하다.
// 항상 익명함수이다.
// 리턴을 생략할 수 있다.
```

<br/>

### 1. **this 바인딩이 다르다**

일반함수의 `this`는 함수 **호출** 방식에 따라 동적으로 결정된다.

> 렉시컬 스코프를 결정하는 것과 헷갈리지 않기! <br/>
> 렉시컬 스코프는 함수 선언 시점에 따라 결정됨.

<br/>

화살표 함수의 `this`는 함수를 **선언**할 때 정적으로 결정되며, 늘 **상위 스코프**의 `this`를 가르킨다.

```javascript
const fn = function () {
	console.log(this);
};
const arrow = () => {
	console.log(this);
};

// 1. 함수 호출
fn(); // Window
arrow(); // Window

// 2. 메소드 호출
const obj = { a: fn };
const objA = { a: arrow };
obj.a(); // obj
objA.a(); // Window

// 3. 메소드 내부 함수 호출
function fnOuter() {
	console.log(this); // person
	function fn() {
		console.log(this); // Window
	}
	fn();
}
var person = { a: fnOuter };
person.a();

function arrowOuter() {
	console.log(this); // person
	const arrow = () => {
		console.log(this); // person
	};
	arrow();
}
var person = { a: arrowOuter };
person.a();

// 4. 콜백함수 호출
const button = document.getElementById("btn");

button.addEventListener("click", function () {
	console.log(this); // button element
});
button.addEventListener("click", () => {
	console.log(this); // Window
});
```

<br/>

### 2. bind, call, apply

일반 함수는 `call`, `bind`, `apply` 함수를 사용하여 `this`를 변경할 수 있지만<br/>
화살표 함수는 언제나 **상위 스코프**의 `this`를 가르킨다.

```javascript
const fn = function () {
	console.log(this);
};
const arrow = () => {
	console.log(this);
};

const name = { name: "blo" };

fn.call(name); // name
fn.apply(name); // name
fn.bind(name)(); // name

arrow.call(name); // Window
arrow.apply(name); // Window
arrow.bind(name); // Window
```

<br/>

### 3. arguments 프로퍼티

일반 함수에는 내장된 `arguments` 라는 객체 프로퍼티가 존재한다.<br/>
`arguments로` 함수 내에서 인자값을 확인할 수 있고 재할당도 가능하다.

```javascript
function foo() {
	console.log(arguments); // Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
	console.log(arguments[0], arguments[2]); // 1, 3

	arguments[0] = "wow";
	console.log(arguments[0]); // wow
}

foo(1, 2, 3);

// 인자를 전달하지 않아도 arguments 객체는 존재한다.
foo(); // Arguments [callee: ƒ, Symbol(Symbol.iterator): ƒ]
```

화살표 함수에서는 `arguments`가 존재하지 않는다.<br/>
대신 `rest paremeters`를 사용할 수 있다.

```javascript
const foo = (...rest) => {
	console.log(rest); // [1, 2, 3]
	console.log(rest[0]); // 1

	rest[0] = "wow";
	console.log(rest); //['wow', 2, 3];
};

foo(1, 2, 3);

// 파라미터의 이름은 마음대로 지을 수 있고
// 직접 지정한(이름지은) 파라미터 외 나머지 후속 파라미터를 rest 파라미터로 설정할 수 있다.
const bar = (a, b, ...others) => {
	console.log(a, b, others); // 1, 2, [3, 4]
};

bar(1, 2, 3, 4);

// 일반 함수의 arguments는 파라미터가 빈값이어도 존재했지만,
// 화살표 함수에서는 파라미터를 이름짓거나 rest 파라미터로 설정해주지 않으면 인자 값을 알 수 없다.
const baz = () => console.log(rest); //ReferenceError: rest is not defined
```

> **arguments와 rest parameter의 차이**
>
> - `arguments`를 실제 배열이 아닌 유사배열. `rest 파라미터`는 실제 배열.<br/>
>     Array 인스턴스의 메서드 사용가능 여부에 차이가 있다.
> - `arguments` 객체는 `callee` 와 같은 기능을 가진 속성이 있다.
> - `arguments`는 함수로 받은 모든 인자를 가르키지만, `rest 파라미터`는 `...rest`로 
> 지정하기 이전에 정의한 파라미터들은 포함하지 않는다.

<br/>

### 4. prototype 프로퍼티와 생성자 함수

<br/>

> **생성자 함수란**
>
> 동일한 프로퍼티를 가지는 **객체**를 반복적으로 **생성**할 수 있는 함수.

생성자 함수는 `prototype` 객체를 이용해 (생성자 함수로 생성된 객체들에게) 프로퍼티와 메서드를 공유하지만, 화살표 함수는 `prototype` 객체를 가지고 있지 않기 때문에 생성자 함수로 사용할 수 없다.

```javascript
function Fn() {}
const Arrow = () => {};

console.log(Fn.prototype); // {constructor: ƒ}
console.log(Arrow.prototype); // undefined
console.log(Fn.hasOwnProperty("prototype")); // true
console.log(Arrow.hasOwnProperty("prototype")); // false
```

<br/>

### 중간 정리

일반 함수와 화살표 함수의 차이는

1. `this` 바인딩이 다르다.
2. `arguments` 프로퍼티 존재 유무가 다르다.
3. 생성자 함수로 사용 가능 여부가 다르다.

그렇다면 함수 내에서 `this`와 `arguments`를 사용하지 않고 생성자 함수로 사용하지 않는데도 일반 함수로 작성되는 이유가 무엇일까?

<br/>

💡차이는 **선언식**과 **표현식**의 **호이스팅**에 있었다.

util 함수나 hook 함수는 일반 함수 중에서도 주로 **선언식**으로 작성이되는데
선언식의 경우 표현식과 다르게 호이스팅이 발생한다.<br/>
화살표 함수의 경우 늘 익명 함수이기 때문에 외부에서 사용할 때는 변수에 할당하여 **표현식**으로 작성된다.

<br/>

### 함수 선언식

선언식은 호이스팅이 발생한다!<br/>
선언식이 현재 스코프의 최상단으로 옮겨지는 것처럼 동작하므로<br/>
**선언하기 전에 함수를 사용할 수 있다!**

```javascript
hoisted(); // "foo"

function hoisted() {
	console.log("foo");
}
```

### 함수 표현식

-   호이스팅되지 않는다.
-   대부분 익명함수를 변수에 할당하는 형태지만, 유명으로 사용할 수 있다.

```javascript
// 1. 호출 후 함수 할당하기
hoisted(); // TypeError: hoisted is not a function

var hoisted = function () {
	console.log("foo");
};
// 표현식은 변수에 함수를 할당하는 형태이므로 변수 자체는 호이스팅 되지만 함수 값이 할당 되는 것은 호출 이후라 함수를 호출할 수 없다는 오류 발생.


// 2. 호출 부를 할당 이후로 옮기기
var hoisted = function () {
	console.log("foo");
};

hoisted(); // "foo"
```

<br/>

### 번외) 변수와 함수의 호이스팅 순서

변수 선언 -> 함수 선언 -> 변수 할당

```javascript
var foo;
function foo() {}
console.log(foo); // f foo() {};

function foo() {}
var foo;
console.log(foo); // f foo() {};
// 함수 선언이 나중에 진행되므로 변수 값을 덮어써 `foo`는 함수값을 가진다.

var foo = "11";
function foo() {}
console.log(foo); // '11'
// 변수를 선언만 하지않고 값 할당까지 이루어졌다면 변수 할당이 최종적으로 진행되기 때문에 `foo`는 할당된 값을 가진다.
```

<br/>

## 📍마무리

`this`, `arguments`, 생성자 함수를 사용하지 않는다면 화살표 함수로 작성이 가능하다.<br/>
다만, 선언식과 표현식의 호이스팅 차이가 있기때문에 선언식으로 작성하면 혹시 모를 사이드 이펙트를 예방할 수 있을 것이다.

<!-- 결론은 일반함수와 화살표 함수의 차이라기 보다는
함수 선언식과 표현식의 호이스팅의 차이가 있었다.

선언식의 경우 루트로 호이스팅되기 때문에
유틸 함수를 안정적으로 호출할 수 있을것이다.

this, arguments 등의 파라미터 사용의 차이가 있는 일반 함수와 화살표 함수의 차이도 있으니 상황에 맞게 사용해서 훅이나 유틸에서도 화살표함수를 사용할 수도 있지만 호이스팅을 고려해 선언식으로 작성한다. -->

#### 참고

-   https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/function
-   https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/function
-   https://velog.io/@skyepodium/JavaScript-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0
-   https://stackoverflow.com/questions/55284165/can-i-use-arrow-functions-instead-of-normal-functions-for-react-hooks/55284550#55284550
-   https://poiemaweb.com/js-this
