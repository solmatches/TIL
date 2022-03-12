# CSS vender prefix와 표준

<br/>

## verder prefix
CSS(Cascading Style Sheet)를 사용하다보면 `vender prefix`(접두사)를 붙인 속성들을 발견할 수 있다.

특히 `display: flex` 같은 속성을 사용할 때  `display: -webkit-box` `display: -ms-flexbox` 등의 접두사가 붙은 속성을 추가시키고는 한다.

### vender prefix의 종류

- `-webkit-` (Chrome, Safari, Opera, 거의 모든 iOS 브라우저, Webkit 기반 브라우저)
- `-moz-` (FireFox)
- `-o-` (Opera 12 이전 버전: 13부터는 Webkit 사용)
- `-ms-` ( Internet Explorer, Microsoft Edge)

### vender prefix 사용 예시
```css
-webkit-transition: all 4s ease;
-moz-transition: all 4s ease;
-ms-transition: all 4s ease;
-o-transition: all 4s ease;
transition: all 4s ease;
```
<br/>

### 왜 사용하는 걸까?

`verder prefix`는 표준이 아닌 css 속성을 브라우저에서 인식할 수 있게 해준다. 

모든 css 속성들이 그렇지만 `flexbox` 를 예로 들자면 많은 변경을 거쳐서 현재의 `display: flex` 표기로 정착이 되었다. 

이전에는 `display: box` 로 사용됐고 `box-*` 로 시작하는 여러 속성이 있었으며 `display: flexbox` 등의 구문으로 쓰이기도 했다. 

최신의 브라우저에서는 이제 적어도 flex를 사용할 때 만큼은 접두사를 사용하지 않아도 되는데, 그럼에도 Internet Explorer 10 의 경우 `display: flexbox` 사양으로 구현되어있고 UC브라우저 (중국의 알리바바가 소유한 Blink 기반 웹 브라우저) 등은 `display: box` 사양으로 구현되어있어 여전히 다양한 브라우저 호환을 위해서는 접두사와 함께 이전 버전의 구문 적용이 필요하다.

> [Autoprefixer Online](https://autoprefixer.github.io/) 에서 이전 버전의 브라우저를 지원하기 위해서 필요한 접두사를 확인할 수 있다.
> 브라우저에서 접두사가 제거된 시기, 버전 정보를 알아보려면 [Can I Use](https://caniuse.com/flexbox)에서 확인 할 수 있다.
> 

> 사실 접두사는 표준에 들어가지 않는 속성들을 자유롭게 ‘실험’해볼 수 있도록 지원하는 것이라 프로덕션에서의 사용을 자제하고 있지만, 많은 프로덕션 웹 사이트에서 그대로 사용되고 있어 브라우저들이 호환성을 보장하고 새로운 기능을 추가하기가 어려워졌다고 한다.
> 

> CSS prefix 이외에도 브라우저 API의 prefix도 제공되고 있는데 CSS prefix와 비슷하지만 접두사 앞뒤의 `-`가 없고 인터페이스의 경우 대문자로 사용되고 속성, 메서드의 경우 소문자로 사용된다.
>

<br/>

```javascript
var requestAnimationFrame = window.requestAnimationFrame ||
                            window.mozRequestAnimationFrame ||
                            window.webkitRequestAnimationFrame ||
                            window.oRequestAnimationFrame ||
                            window.msRequestAnimationFrame;
```
<br/>

## CSS 표준이란?

사실 flex는 완전한 표준화 속성이 아닌 Candidate Recommendation (권장사항 후보) 단계라고 한다. [(관련문서)](https://www.w3.org/TR/css-flexbox-1/) 

*아니 아직 표준이 아니었다니!*

### 그렇다면 CSS 표준은 어떻게 정해지는걸까?

[W3C 명세](https://www.w3.org/Style/CSS/#specs) : W3C에서는 CSS의 모든 사양을 문서화하여 제공하고있으며, 이전의 사양이나 최신 기술에대한 논의도 W3CWG (Working Group)을 통해 계속해서 이루어지고 있다.

- 1996년 W3C의 주도하에 `CSS Level 1`이 발표되었고 당시 주요한 브라우저인 넷스케이프, 인터넷 익스플로러가 해당 버전을 지원하였다.
- 1998년 `CSS Level 2`가 발표되었고 대부분의 웹 브라우저들이 이를 지원하였다.
- `CSS Level 1` `CSS Level 2` 는 해당 레벨을 정의한 하나의 문서였으나 `CSS Level 3` 부터는 모든 사양을 하나씩 정리하는대신 모듈 단위로 개발되기 시작했다. 
모듈식은 브라우저에서 최신 CSS를 모듈 단위로 구현하고 업데이트 함으로써 최신 CSS의 지원을 빠르게 대응할 수 있도록 하기위한 방법이라고한다.

### 표준화를 위한 단계

해당 [문서](https://www.w3.org/2020/Process-20200915/#rec-track)에 따르면 표준으로 지정되기 위한 단계는 총 6단계이다. (더 세세하게 나눠진 단계도 있다. 문서에서 확인 가능.)

1. [FPWD] First Public Working Draft - 첫 번째 공개 작업 초안 게시.
2. **[WD] Working Draft - 작업 초안 게시. (설계, 구현 테스트)**
3. **[CR] Candidate Recommendation - 권장 후보 게시. ( 사양 테스트, 구현 보고서 생성)**
4. [PR] Proposed Recommendation - 최종 권장사항 게시.
5. **[REC] Recommendation - W3C 권장사항으로 게시.**
6. [AR] Amended Recommendation - 수정된 권장사항.

‘표준’ 이 되는 단계는 5번인 REC 단계부터이다. 3번 CR 단계부터 일상적인 사용을 권장한다.

프로세스는 대략 2, 3, 5번의 3단계로 요약할 수 있다.

> [해당 문서](https://cssdb.org/#the-staging-process)에 따르면 실제로 W3C의 사양과 브라우저의 구현은 동기화 되지 않는다고 한다. ( 표준 권장일 뿐 브라우저에서 반드시 구현되도록 강제하는 규칙이 아니다)

> 브라우저별 CSS 지원 속성, 지원률 확인은 [이 사이트](https://css3test.com/) 에서 할 수 있다.
>

<br/>

## 참고

- [https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix)
- [https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Backwards_Compatibility_of_Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Backwards_Compatibility_of_Flexbox)
- [https://developer.mozilla.org/ko/docs/Web/CSS](https://developer.mozilla.org/ko/docs/Web/CSS)
- [https://cssdb.org/#the-staging-process](https://cssdb.org/#the-staging-process)
- [https://seulbinim.github.io/WSA/css-basic.html#css란](https://seulbinim.github.io/WSA/css-basic.html#css%EB%9E%80)
- [https://www.w3.org/Style/CSS/current-work](https://www.w3.org/Style/CSS/current-work)
- [https://www.w3.org/2020/Process-20200915/#rec-track](https://www.w3.org/2020/Process-20200915/#rec-track)
- [https://fantasai.inkedblade.net/weblog/2011/inside-csswg/process](https://fantasai.inkedblade.net/weblog/2011/inside-csswg/process)
- [https://css3test.com/](https://css3test.com/)