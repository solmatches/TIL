# React.FC vs React.VFC

타입스트립트를 사용하면서 함수형 컴포넌트를 만들다보면 `React.FC` 라는 타입을 컴포넌트에 명시하여 사용하고는 한다. 그런데 `FC`와 관련된 이슈를 발견하였다.

<br/>

### FC의 문제점

`FcunctionComponent`의 줄임말인 `FC`는 아래와 같이 정의되어있다.

```tsx
type FC<P = {}> = FunctionComponent<P>;

interface FunctionComponent<P = {}> {
    (props: PropsWithChildren<P>, context?: any): ReactElement<any, any> | null;
    propTypes?: WeakValidationMap<P> | undefined;
    contextTypes?: ValidationMap<any> | undefined;
    defaultProps?: Partial<P> | undefined;
    displayName?: string | undefined;
}
```

여기서 `PropsWithChildren<P>` 이 부분에서 children props를 가지고 있다는 것을 명시하는데, 컴포넌트가 실제로 `children` props를 가지고 있지 않지 않을 때도 컴포넌트는 암묵적으로 `children`을 가지고 있는 컴포넌트로 설계가 된다.

컴포넌트가 `children`을 사용하지 않아도 `children`을 넘길 수 있게 되어버리는 것이다.

<br/>
그밖에도 문제점들이 이야기되는데 아래의 CRA의 `Typescript template`에서 `React.FC`가 제거된 문제 제기를 보면 확인할 수 있다.

[https://github.com/facebook/create-react-app/pull/8177](https://github.com/facebook/create-react-app/pull/8177)

대략 문제를 정리하자면

- 컴포넌트에 제네릭을 설정하는데 제약이 있음
- `<Select.Item/>`과 같이 컴포넌트 네임스페이스를 사용하는 경우 타입 지정이 어색해진다. (쓸데없이 복잡해진다)
- `defaultProps` 사용시 제대로 동작하지 않는다.
- 코드가 길어진다. 코드 작성 시간도 길어진다.

<br/>

### FC의 장점

- 반환 유형을 명시적으로 제공한다.

```tsx
const Component = () => undefined; 
// 컴포넌트는 정의되지 않은 값을 반환할 수 없다. `null`은 가능.

const Page = () => <Component/>;
// Component가 잘못된 값을 반환하기 때문에 오류가 발생하므로 
// 어차피 사용할 때 알아차리게 될 것이다. 
// 그러나 타입을 명시하게 되면

const Component1 = (props: ComponentProps):JSX.Element => {/*...*/}
const Component2:FC<ComponentProps> = (props) => {/*...*/}
// FC가 반환 유형을 명시해주므로 코드가 더 간결하다.
```

위와 같은 문제로 요새는 FC를 사용하지 않는 추세라고 한다.

> FC를 지정하는 것에 익숙해져서 생각해보지 못한 부분이었는데, 컴포넌트에서 children을 사용하지 않거나 props를 사용하지 않는 경우 구지 FC를 사용하지 않는 것이 보기에 간결하고 사용성도 좋은 것 같다!
> 

<br/>

### 그런데 `VFC` 라는게 있던데?

`FC`에서는 children을 암묵적으로 정의하기 때문에 `children`을 사용하지 않는 컴포넌트는 `VFC`로 사용한다고 한다.

`VFC`의 타입 명세는 아래와 같다.

```tsx
type VFC<P = {}> = VoidFunctionComponent<P>;

interface VoidFunctionComponent<P = {}> {
    (props: P, context?: any): ReactElement<any, any> | null;
    propTypes?: WeakValidationMap<P> | undefined;
    contextTypes?: ValidationMap<any> | undefined;
    defaultProps?: Partial<P> | undefined;
    displayName?: string | undefined;
}
```

`FC`와 비교하면 다른 부분은 같고 `props: PropsWithChildren<P>` 가 `props: P`로 되어있다.

eslint-plugin-react의 이슈에서 끔찍한 이름이라고 하는 걸 보고 조금 웃겼다. 

[https://github.com/yannickcr/eslint-plugin-react/issues/2913](https://github.com/yannickcr/eslint-plugin-react/issues/2913)

아래 내용을 더 읽어보니 React의 다음 릴리즈에서는 `VFC`가 제거될 것이라고 한다. 대략보니 `FC`를 제거하고 `VFC`를 `FC`로 사용하는 것 같다. 그렇다면 children을 사용할 때는 따로 정의해서

```tsx
interface ComponentProps {
	children: React.ReactElement;
}
type ComponentProps = PropsWithChildren<P>
```

둘 중 하나의 방법을 선택하면 될 것 같다!

## 참고

- [https://www.mydatahack.com/using-react-vfc-instead-of-react-fc/](https://www.mydatahack.com/using-react-vfc-instead-of-react-fc/)
- [https://github.com/yannickcr/eslint-plugin-react/issues/2913](https://github.com/yannickcr/eslint-plugin-react/issues/2913)
- [https://github.com/facebook/create-react-app/pull/8177](https://github.com/facebook/create-react-app/pull/8177)