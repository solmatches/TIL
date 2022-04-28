# React Testing Library
-----

## 사용하는 이유
사용자가 애플리케이션을 사용할 때 우리의 의도대로 동작하는 것에 대한 확신을 가지기 위해 테스트 코드를 작성한다.

즉, ‘사용자의 액션 → 제대로 작동’ 하는지를 테스트 하는 것에 집중하기 위함이다.

react testing library는 행위 주도 테스트(Behavior Driven Test) 방법론이 대두되면서 주목 받기 시작했다. 
행위주도 테스트는 예를들어 제목이 `h2`태그로 작성되었는지 `class`가 `title`로 사용되었는지에는 관심이 없다 오로지 화면에 ‘제목’ 이라는 텍스트가 보이는지에만 관심이 있다. 
사용자 입장에서는 제목 h2가 아닌 h3 태그로 작성되어도 스타일이 조금 달라보일 뿐 제목이 있다는 사실은 변함이 없다.

RTL이 등장하기 이전에는 Enzyme이란 라이브러리가 많이 사용되었다.

Enzyme은 RTL과 달리 위에서 설명한 제목이 h2태그로 작성되었는지 어떤 props를 받고 있는지와 같이 구현 주도 테스트(Implementation Driven Test)에 적합하다. 또한 실제 브라우저 DOM이 아닌 React의 가상 DOM을 기준으로 테스트를 작성해야한다. 

RTL은 `jsdom`라이브러리 를 통해 실제 브라우저 DOM을 기준으로 테스트를 작성한다.

<br />

## 컴포넌트 테스트 제외 사항

[스토리북 문서 내 테스트 전략](https://storybook.js.org/tutorials/design-systems-for-developers/react/ko/test/) 을 보면 아래와 같은 테스트는 비효율적이라고 한다.

- 스냅샷 테스트 (JEST) - 마크업 보다는 렌더링을 테스트 해야함
- end to end 테스트 - 사용자의 작업 절차를 확인하는 등 복잡한 프로세스 검증에 유용함. 컴포넌트는 비교적 단순하므로 절차 테스트 작성 시간이 더 오래 걸리며 비효율적이게 됨

<br />

## 테스트 케이스 만들기

테스트해야 할 항목을 명확하게 작성하자.

- 앱의 사용자 입장에서 반드시 확인해야 하는 항목은 무엇인가

버튼 테스트이므로 버튼이 가진 속성별로 비활성화, 테두리 등 렌더가 잘 되는지 확인하는 테스트를 작성해보자.

- **`should render a disabled button` → `expect(button).toBeDisabled()` // `@testing-library/jest-dom`**
- `**should render a button with the outline of white**`
- `**should render a floating button**`
- 버튼 클릭 이벤트 발생 // `**@testing-library/user-event`** 사용자가 컴포넌트와 상호작용하는 것 처럼 테스트 가능

<br />

## 작성방법 [#](https://tecoble.techcourse.co.kr/post/2021-10-22-react-testing-library/)

- 타켓유형에는 우선 순위가 있음 [#](https://testing-library.com/docs/queries/about/#priority)
- action은 userEvent를 권장함 [#](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library#not-using-testing-libraryuser-event)
- role찾는 팁 [#](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library#not-using-byrole-most-of-the-time)

<br/>

## 참고
- [https://sangboaklee.medium.com/react-컴포넌트-테스트에-대한-생각-6ec4b234b8eb](https://sangboaklee.medium.com/react-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%ED%85%8C%EC%8A%A4%ED%8A%B8%EC%97%90-%EB%8C%80%ED%95%9C-%EC%83%9D%EA%B0%81-6ec4b234b8eb)
- [https://jbee.io/react/testing-1-react-testing/](https://jbee.io/react/testing-1-react-testing/)
- [https://team.modusign.co.kr/프론트엔드에서-의미있는-테스트-코드-작성하기-4992409c7f2d](https://team.modusign.co.kr/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%EC%97%90%EC%84%9C-%EC%9D%98%EB%AF%B8%EC%9E%88%EB%8A%94-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1%ED%95%98%EA%B8%B0-4992409c7f2d)
- [https://www.softwaretestinghelp.com/what-is-component-testing-or-module-testing/](https://www.softwaretestinghelp.com/what-is-component-testing-or-module-testing/)
- [https://www.daleseo.com/react-testing-library/](https://www.daleseo.com/react-testing-library/)
- [https://testing-library.com/docs/react-testing-library/example-intro](https://testing-library.com/docs/react-testing-library/example-intro)

### 스토리북에서 제공하는 컴포넌트 테스트

[https://storybook.js.org/tutorials/intro-to-storybook/react/en/composite-component/](https://storybook.js.org/tutorials/intro-to-storybook/react/en/composite-component/)

### 디자인 시스템 관련 시각적 회귀 테스트

[https://ideveloper2.dev/blog/2021-01-24--시각적-회귀-테스트-visual-regression-test/](https://ideveloper2.dev/blog/2021-01-24--%EC%8B%9C%EA%B0%81%EC%A0%81-%ED%9A%8C%EA%B7%80-%ED%85%8C%EC%8A%A4%ED%8A%B8-visual-regression-test/)
