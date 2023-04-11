# NX vs Rush vs Lerna

<br/>

## Lerna

모노레포로 이루어진 자바스크립트 프로젝트를 관리하기위한 도구

- 심링크로 내부 의존성을 연결하여 중복된 패키지 제거 가능
- 수동으로 버전 결정 가능
- 커밋 메세지로 패키지 버전을 결정할 수있음.
- 버전 업데이트 후 관련 커밋 메세지를 모아 CHANGELOG가 자동 생성됨

```bash
# 수동으로 버전 결정
$ lerna version 1.0.1 # 지정된 버전으로 업데이트
$ lerna version patch # semver 키워드를 사용하여 업데이트
$ lerna version       # prompt에서 선택하여 업데이트

# 커밋 메시지에 따라 버전 결정하기
$ lerna version --conventional-commits # 커밋 메시지를 분석하여 버전 업데이트
```

- 마지막 커밋과 차이가 있는 패키지(`@repo/utils`)를 파악해서 버전을 올려준다.
이때 버전이 변경된 패키지를 종속성으로 사용하고 있는 패키지(`@repo/components`)가 있다면 해당 패키지의 버전도 올려준다.

```json
# @repo/utils의 버전이 업데이트 됨
{
	"name": "@repo/utils",
	"version": "1.0.1" -> "1.0.2"
}

# @repo/components의 버전이 변경되고 
# components에서 사용되고 있는 @repo/utils의 버전도 업데이트 된 버전으로 변경됨
{
	"name": "@repo/components",
	"version": "2.5.10",
	"dependencies":{
		"@repo/utils": "^1.0.2"
	}
}
```

- 빌드 속도가 느리다.

## NX

모노레포에서 필요한 기능들을 제공해준다. 프로젝트 구조를 특정 구조로 강제하며 라이브러리 별 best practice가 캡슐화된 plugin들을 사용하여 개발자 경험을 올려준다.

- 내부 설정파일을 이용하여 의존성을 관리한다

장점

- React, Next.js, Storybook 등 여러 라이브러리나 프레임 워크들에 대한 패키지 생성 기능 (빠른 프로젝트, 컴포넌트 생성)
- 빌드, 테스트, 린트 등의 명령어를 실행할 수 있는 플러그인 제공

단점

- vscode 에서 라이브러리를 한번은 수동으로 가져와야 인식한다.
- 표준이 아닌 자체 빌더에 의존하며 빌더를 Webpack을 사용하도록 강제.
- 루트에 단일 `package.json` (모노레포에서 하나의 라이브러리 버전만 사용할 수 있음. 모든 의존성을 루트에 설치해야함)
- 지원하지 않는 라이브러리, 프레임워크를 사용해야 한다면 nx플러그인을 직접 만들어야한다. 그러나 플러그인 풀이 아주 풍부한 편
- 버전 관리, Changelog 생성 기능 없음

## RUSH

MS에서 만든 웹을위한 확장 가능한 모노레포 관리 도구

- 심링크로 내부 의존성 연결. 즉각적인 변경사항 반영, 중복된 패키지 제거 가능
- 프로젝트에 변경사항이 있는지 감지하고 적절한 버전 넘버를 자동으로 업데이트 해준다.
- PR이 생성될 때마다 SEMVER 을 제공하도록 설정할 수 있음. 이 후 CHANGELOG 파일이 생성됨
- 모든 프로젝트에서 사용하는 의존성 버전 통일 가능
- 개인 이메일주소 사용 제어 가능
- pnpm 사용을 권장 (yarn, npm 사용 가능)

## 참고

[https://www.reddit.com/r/node/comments/r5e9o0/nx_vs_lerna_vs_rush_can_anyone_comment_on_their/](https://www.reddit.com/r/node/comments/r5e9o0/nx_vs_lerna_vs_rush_can_anyone_comment_on_their/)

## 참고

[모던프론트엔드 프로젝트 구성기법 - 모노레포 개념편](https://d2.naver.com/helloworld/0923884)

- 모놀리식 → 모듈화 없음. 관심 분리가 어렵고 서로 직접적으로 의존하는 코드들. 리팩토링, 배포등의 작업이 커지고 비효율적이다.
- 모듈화 → 모놀리식의 단점을 개선한 방법. 만약 모듈을 다른 프로젝트에서도 사용한다면 소스를 어디에 위치해야할까?
- 멀티레포 → 공용 모듈을 독자적 저장소에서 관리한다. 독립적인 개발, 린트, 테스트, 빌드, 게시, 배포 파이프라인이 존재한다.
- 번거로운 프로젝트 생성, 패키지 중복 코드, 파이프라인에 대한 관리 포인트 증가
- 독자적으로 관리되기 때문에 일관적인 개발자 경험이 보장되지 않음. 각 프로젝트의 명령어를 별도로 기억해야한다
- 관련된 프로젝트의 변경사항을 따로 파악해야한다. ( 놓칠 가능성 
- 관련 프로젝트의 버전관리 와 리팩토링 비용
- 모노레포 → 두개 이상의 프로젝트가 동일한 저장소에 저장되는 개발 전략
단순히 여러 프로젝트가 하나의 저장소를 사용한다고 모노레포라고 부르기에는 부족하다.
    - 프로젝트 사이에 의존성이 존재하거나 같은 제품군이거나 하는 정의된 관계가 필요하다.
    
    모노레포가 해결하는 멀티레포의 문제
    
    - 쉬운 프로젝트 생성
        - 개발환경, CI/CD, 빌드, 게시 등을 기존 저장소를 이용
        - 의존성 패키지가 같은 저장소에 있으므로 의존성 관리가 쉽다.
        - 개발 환경에 대한 업데이트를 모든 프로젝트에 한 번에 반영할 수 있다.
        - 일관된 개발자 경험 제공
        - 관련된 프로젝트의 업데이트를 한번에 커밋하고 이슈 확인을 통합하여 할 수 있다.
        - 소스 변경시 모든 프로젝트를 다시 빌드하지 않아도 된다. 변경 사항의 영향을 받는 프로젝트만 테스트하고 빌드한다.
        

[모던프론트엔드 프로젝트 구성기법 - 모노레포 도구편](https://d2.naver.com/helloworld/7553804)