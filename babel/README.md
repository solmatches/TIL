# regeneratorRuntime Error

(![regeneratorRuntime Error](img/1-regenerator-runtime-error.png))

<br/>

## 문제 발생

아래와 같이 async await을 사용해 api 통신을 하는데 `ReferenceError: regeneratorRuntime is not defined` 에러가 발생했다.

```tsx
function Main() {
	useEffect(() => {
		async function calling() {
			try {
				const result = await api.get("/api/a/b");
				return result;
			} catch (error) {
				console.log("error: ", error);
				return error;
			}
		}
		calling();
	}, []);
}
```

![above-error-component](img/2-above-error-component.png)

바로 아래 해당 오류가 어디에서 발생했는지 추적할 수 있도록 경로가 나와있어 들어가보았다.

![regenerator-runtime-source-code](img/3-regenerator-runtime-source-code.png)

내가 호출한 `calling` 함수를 바벨이 `_asyncToGenerator`함수를 사용해 재정의한 후 반환하는데, 인자로`regeneratorRuntime`을 사용한 콜백함수를 전달하고 있는 모습을 확인할 수 있다.

> 비동기 작업을 변환하는데 `switch`를 사용하고 있다니 신기.. [여기](https://yceffort.kr/2021/03/javascript-generator-regeneratorRuntime)에서 설명한 글을 읽고나니 대략 어떻게 동작하는지 감을 잡을 수 있었다!

[바벨 공식문서](https://babeljs.io/docs/en/babel-plugin-transform-runtime#regenerator-aliasing)에 따르면 `generator` (생성기) 함수나 비동기 함수를 사용할 때마다 `regeneratorRuntime`를 사용한 코드로 변환되는데, (`@babel/preset-env` 안에 포함된 `@babel/plugin-transform-regenerator`를 이용한다.) 이 변환된 코드를 브라우저가 인식하지 못하는 것이다.

<br />

## 해결 방법

`regeneratorRuntime`을 지원할 수 있도록 폴리필을 설치하면 된다!

이전에는 `@babel/polyfill` 플러그인에 포함되어있는 `regenerator-runtime/runtime`를 이용해 지원했었고
이 후 바벨 v7.0.0 부터 `@babel/preset-env` 이 `@babel/polyfill` 을 대체하게되고
v7.4.0 부터 `"core-js/stable"`와 `"regenerator-runtime/runtime"`가 플러그인에서 제외되면서 별도로 폴리필을 설치하여 해주어야한다.

> [@bable/preset-env 에 포함된 플러그인 목록](https://github.com/babel/babel/blob/main/packages/babel-compat-data/scripts/data/plugin-features.js) 을 보면 `regeneratorRuntime` 으로 변환해주는 `transform-regenerator`은 있지만 이를 보편적으로 사용하도록 해석해주는 `regenerator-runtime` 플러그인은 없다.

방법은 두 가지가 있다.

### 방법 1. \***\*@babel/plugin-transform-runtime 사용\*\***

### 방법 2. @babel/preset-env 의 useBuiltIns, corejs 옵션 사용

나는 \***\*@babel/plugin-transform-runtime 을 적용하였고\*\***

![regeneratorRuntime Compile](img/4-regenerator-runtime-source-code-after.png)

적용 후 `regeneratorRuntime` 부분이 `_babel_runtime_regenerator__WEBPACK_IMPORTED_MODULE_!___defualt()` 로 컴파일되었다!

앱을 실행해보니 잘 실행되었다

<br/>
<br/>

## 참고

-   [https://babeljs.io/docs/en/babel-plugin-transform-regenerator](https://babeljs.io/docs/en/babel-plugin-transform-regenerator)
-   [https://babeljs.io/docs/en/babel-plugin-transform-runtime](https://babeljs.io/docs/en/babel-plugin-transform-runtime#regenerator-aliasing)
-   [https://andrew-flower.com/blog/Async-Await-with-React](https://andrew-flower.com/blog/Async-Await-with-React)
-   [https://yceffort.kr/2021/03/javascript-generator-regeneratorRuntime](https://yceffort.kr/2021/03/javascript-generator-regeneratorRuntime)
-   [https://github.com/babel/babel/blob/main/packages/babel-compat-data/scripts/data/plugin-features.js](https://github.com/babel/babel/blob/main/packages/babel-compat-data/scripts/data/plugin-features.js)
-   [https://github.com/zloirock/core-js#commonjs-api](https://github.com/zloirock/core-js#commonjs-api)
