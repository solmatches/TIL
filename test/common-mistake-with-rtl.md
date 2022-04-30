# RTL을 사용할 때 도움이되는 팁

## [Common mistakes with React Testing Library](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library) 블로그에서 권장하는 내용을 정리

- eslint 플러그인을 사용해라 (일반적인 실수들을 피하고 싶다면 많은 도움이 될 것)
    - eslint-plugin-testing-library
    - eslint-plugin-jest-dom
- render의 리턴값을 변수에 할당할 때 `view` 를 쓰거나 구조분해 할당 사용하기
    - `wrapper`라고 쓰지 말아라 그런 명칭은 (enzyme)의 레거시이며 render의 리턴값은 아무것도 감싸지 않는다.
- `cleanup` 은 자동으로 이루어지고 있으므로 더이상 사용하지 않아도 된다.
- `@testing-library/jest-dom` 을 설치해서 jest-dom의 단언문을 사용하기
    - 훨씬 나은 에러메세지를 받을 수 있다.
- 불필요하게 `act`로 감싸지 않아도 `render`, `fireEvent`는 항상 `act`로 감싸져 있다.
    - `act` 경고문을 본다면 기대하지 않은 일이 테스트에서 발생하고 있다는것을 말해주는 것일 뿐. [여기](https://kentcdodds.com/blog/fix-the-not-wrapped-in-act-warning)에서 배울 수 있다.
- [어떤 쿼리를 써야하나요?](https://testing-library.com/docs/queries/about/#priority) 페이지를 통해 올바른 쿼리 사용하기
- `screen.getByRole("button", { name: /submit/i });` 와 같이 name 옵션을 통해 버튼의 텍스트로 쿼리하기
    - 동의하지 않는 사람들이 있지만 만든사람은 권장한다.
    - 버튼 텍스트가 바뀌는 경우 테스트 코드는 당연히 고쳐져야한다. 바꾸는 비용도 매우 낮고 테스트도 더 읽고 쓰기 쉬워진다.
- `*ByRole`사용하기
    - 스크린 리더가 읽을 수 있는 이름으로 쿼리할 수 있다.
    - 에러가 발생하는 경우 전체 DOM의 로그를 남기고, 사용가능한 `role` 에 대한 로그도 남겨주어 디버깅이 편리하다.
    - 버튼을 특정지으려고 `role=button` 과 같이 속성을 추가하지 않아도 된다.
    - [role 리스트](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles)
- `@testing-library/user-event` 사용하기
    - fireEvent 기반으로 만들어진 패키지이지만, 사용자 상호작용과 더 유사한 메서드들을 제공한다.
    - 유저가 실행할 모든 동일한 이벤트를 실행하도록 계속 빌드 중. 추후 `@testing-library/dom`에 포함시킬수도 있음
- `query*`는 엘리먼트를 찾을 수 없다는 단언을 할 때만 사용하기
    - `query*`는 엘리먼트가 페이지에 렌더링이 되지 않았는지 확인할 때만 유용하다
    - 엘리먼트가 없을 때 에러를 발생시키지 않는다.
- 즉시 사용할 수 없는 무언가를 쿼리하고 싶을 때는 `waitFor`보다 `find*` 사용하기
    - `find*`쿼리는 내부에서 `waitFor`를 사용한다.
    - 두개다 근본적으로는 같지만 `find*`를 통해 더 간단하고 나은 에러메세지를 받을 수 있다.
- `waitFor` 콜백에 단언문을 넘겨주기
    - `waitFor`의 목적은 특정한 것이 일어날 때 까지 기다리는 것.
    - 빈 콜백을 넘겨주는 경우 비동기 로직을 리랙토링 하는 경우 쉽게 실패하는 취약한 테스트가 남는다.
- `waitFor` 콜백에는 하나의 단언문만 넣기
    - 비동기 테스트를 한다는 가정하에, 다중 단언문을 넣으면 테스트 실패를 확인하기위해 타임아웃을 기다려야함.
    - 하나만 넣는 경우 하나가 실패하면 더 빨리 실패를 확인할 수 있음.

```jsx
// ❌
await waitFor(() => {
  expect(window.fetch).toHaveBeenCalledWith("foo");
  expect(window.fetch).toHaveBeenCalledTimes(1);
});
// ✅
await waitFor(() => expect(window.fetch).toHaveBeenCalledWith("foo"));
expect(window.fetch).toHaveBeenCalledTimes(1);
```

- `waitFor` 콜백 바깥에 사이드 이펙트를 넣고 콜백안에는 단언문만 사용하기
    - `waitFor`는 수행한 작업과 단언문 전달 사이에 비동기 테스트를 위한 것.
    - 콜백은 비동기 횟수과 빈도로 호출될 수 있으며 사이드 이펙트가 여러번 실행될 수 있다는 뜻.
    - `waitFor` 내부에 스냅샷 단언문을 사용하고 싶다면 우선 특정 단언문을 기다리고 그 다음 스냅샷을 찍으면 된다.

```jsx
// ❌
await waitFor(() => {
  fireEvent.keyDown(input, { key: "ArrowDown" });
  expect(screen.getAllByRole("listitem")).toHaveLength(3);
});
// ✅
fireEvent.keyDown(input, { key: "ArrowDown" });
await waitFor(() => {
  expect(screen.getAllByRole("listitem")).toHaveLength(3);
});
```

- 쿼리후 엘리먼트가 존재함을 단언하고 싶으면 단언문을 명시적으로 만들기
    - 만약 `get*`쿼리들이 엘리먼트를 찾는데 실패했다면 쿼리들은 디버깅에 도움이 될 수 있도록 DOM 구조를 보여여주는 유용한 에러메세지를 던질 것이다.
    - 쿼리 후 단언문을 건너뛰어도 괜찮지만, 엘리먼트가 존재함을 명확하게 알려주기 위해 단언문을 유지하는 것을 추천.


## 참고
- [Common mistakes with React Testing Library](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library)
- [한국어 번역판](https://seongry.github.io/2021/06-20-common-mistakes-with-rty/)