## 한글 초성, 중성, 종성 추출하기

---

> 한글의 초성을 알아내려면 어떻게 해야할까?

영어의 경우에는 `var word = 'alphabet'` 중 `word[0]`를 사용하면 첫 번째 글자를 쉽게 알아낼 수 있다.

하지만 한글의 경우에는 하나의 글자를 만들 때 초성, 중성, 종성을 조합할 수 있다.

초성, 중성, 종성을 분리하는 방법을 알아보자.

---

[mdn 문서](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#unicode) 에 따르면

javascipt에서는 대소문자를 구분하여 `유니코드` 문자 집합을 사용한다고 한다.

그럼 문자인 한글도 유니코드로 이루어져 있을것이다. 각각의 유니코드를 어떻게 알 수 있을까?

String에 `charCodeAt` 메서드를 이용하는 방법이 있다고 한다.

> `charCodeAt()` 메서드는 인자로 인덱스를 전달할 수 있고, 인덱스에 해당하는 `0~65535`사이의 UTF-16 코드를 나타내는 정수를 반환한다.
> `charAt()` 문자열에서 주어진 인덱스에 해당하는 유니코드 단일문자를 반환한다.
> `String.fromCharCode()` UTF-16 코드를 인자로 받아 문자열을 생성해 반환한다.
> String의 정적 메소드이기 때문에 `String`을 붙여주어야 한다.

```javascript
const text = "hello world";
const index = 4;

console.log(text.charCodeAt(index), text.charAt(index)); // 111, 'o'
```

다시 한글로 돌아와서 한글의 유니코드를 살펴보자.

초성: 19자

> ['ㄱ', 'ㄲ', 'ㄴ', 'ㄷ', 'ㄸ', 'ㄹ', 'ㅁ', 'ㅂ', 'ㅃ', 'ㅅ', 'ㅆ', 'ㅇ', 'ㅈ', 'ㅉ', 'ㅊ', 'ㅋ', 'ㅌ', 'ㅍ', 'ㅎ']

중성: 21자

> ['ㅏ', 'ㅐ', 'ㅑ', 'ㅒ', 'ㅓ', 'ㅔ', 'ㅕ', 'ㅖ', 'ㅗ', 'ㅘ', 'ㅙ', 'ㅚ', 'ㅛ', 'ㅜ', 'ㅝ', 'ㅞ', 'ㅟ', 'ㅠ', 'ㅡ', 'ㅢ', 'ㅣ' ];

종성: 28자

> ['', 'ㄱ', 'ㄲ', 'ㄳ', 'ㄴ', 'ㄵ', 'ㄶ', 'ㄷ', 'ㄹ', 'ㄺ', 'ㄻ', 'ㄼ', 'ㄽ', 'ㄾ', 'ㄿ', 'ㅀ', 'ㅁ', 'ㅂ', 'ㅄ', 'ㅅ', 'ㅆ', 'ㅇ', 'ㅈ', 'ㅊ', 'ㅋ', 'ㅌ', 'ㅍ', 'ㅎ']

한글은 총 (19x21x28) `11,172`자로 이루어져 있고
유니코드 `0xAC00`(가)에서 부터 10진수로 `44032~55203` 까지의 숫자라고 한다.

그래서 한글을 구하는 식은

```javascript
var 가 = parseInt("0xAC00", 16);
var 중성개수 = 21;
var 종성개수 = 28;

var 초성 = (str) => (str.charCodeAt(0) - 가) / 종성개수 / 중성개수;
var 중성 = (str) => ((str.charCodeAt(0) - 가) / 종성개수) % 중성개수;
var 종성 = (str) => (str.charCodeAt(0) - 가) % 종성개수;

// 위의 코드는 각 글자의 유니코드 값이 아니라
// 초성이면 0~18 인덱스 중에 몇 번째인지, 중성이면 중성중에 몇 번째인지를 나타내기 때문에 처리가 더 필요하다.

// var 초성첫문자 = "ㄱ".charCodeAt(0); // 이렇게 쓰지 않는 이유. 'ㄱ'(12593), 'ᄀ'(4352) 이 다르다!
// 키보드의 'ㄱ', 'ᄀ'이 언뜻 보기에는 비슷해 보여도 유니코드상 다르기 때문에 다른 문자가 반환되는 문제가 발생한다.
var 초성첫문자 = parseInt("0x1100", 16); // 'ᄀ'
var 중성첫문자 = parseInt("0x1161", 16); // 'ㅏ'
var 종성첫문자 = parseInt("0x11A8", 16) - 1; // '' // 종성이 없는 경우가 있으므로 1을 뺀다.

var 초성유니코드 = 초성 + 초성첫문자;
var 중성유니코드 = 중성 + 중성첫문자;
var 종성유니코드 = 종성 + 종성첫문자;

function get초성(src) {
	var 초성글자 = "";
	for (var i = 0; i < src.length; i++) {
		var index = 초성(src[i]);
		초성글자 += String.fromCharCode(index + 초성첫문자);
	}
	return 초성글자;
}

console.log(get초성("안녕하세요")); // 'ㅇㄴㅎㅅㅇ'

function get중성(src) {
	var 중성글자 = "";
	for (var i = 0; i < src.length; i++) {
		var index = 중성(src[i]);
		if (index >= 0) {
			중성글자 += String.fromCharCode(index + 중성첫문자);
		}
	}
	return 중성글자;
}
function get종성(src) {
	var 종성글자 = "";
	for (var i = 0; i < src.length; i++) {
		var index = 종성(src[i]);
		if (index > 0) {
			종성글자 += String.fromCharCode(index + 종성첫문자);
		}
	}
	return 종성글자;
}

console.log(get중성("안녕하세요")); // 'ᅡᅧᅡᅦᅭ'
console.log(get종성("안녕하세요")); // 'ᆫᆼ'
```

#### 참고

-   https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#unicode
-   https://developer0809.tistory.com/174
-   https://goni9071.tistory.com/164
-   https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt
-   https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/fromCharCode
-   https://gist.github.com/sooop/4958873
