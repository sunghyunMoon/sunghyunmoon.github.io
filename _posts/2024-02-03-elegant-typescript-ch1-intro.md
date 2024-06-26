---
title: 1장 들어가며
categories:
- React Deep Dive
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/elegant_typescript.png"
---

### 1.1 웹 개발의 역사

#### 1.1.2 자바스크립트 표준, ECMA Script의 탄생

- 그러면 어떻게 자바스크립트가 브라우저에서 널리 사용되기 시작했을까? 경쟁 관계이던 넷스케이프와 마이크로소프트는 자신들의 브라우저에 새로운 기능을 빠르게 늘리기 시작했는데 이렇게 추가된 기능은 각자의 브라우저에서만 동작했다. 특히 인터넷 익스플로러와 내비게이터의 DOM 구조는 완전히 다르기 때문에 브라우저마다 웹 페이지가 다르게 동작하거나 제대로 동작하지 않는 크로스 브라우징Cross Browsing 이슈가 발생했다. 따라서 개발자는 어 떤 기능을 추가하기 위해서는 두 개의 스크립트를 따로 개발해야 하는 어려움을 겪어야만 했다.
- 또한 초기의 자바스크립트는 브라우저 생태계를 고려해서 작성된 것이 아니었고 단지 새로운 기능이 추가되는 형태로 발전했다. 이에 따라 자바스크립트와 브라우저의 발전 속도 간의 차이가 나기 시작했고 결국 브라우저는 자바스크립트의 변화를 따라가지 못했다. 자바스크립트 에 어떤 기능이 추가된다면 런타임 환경인 브라우저도 이 기능을 지원할 수 있어야 한다.
- 새로운 버전의 브라우저가 출시되어 자바스크립트의 새로운 기능을 지원하더라도 사용자가 예전 버전의 브라우저를 사용한다면 이 기능은 무용지물이 된다. 이런 문제를 해결하기 위해 자바스크립트에 폴리필(Polyfill)과 트랜스파일(transpile) 같은 개념이 등장하기도 했다.
    - 풀리필（Polyfill） 과 트랜스파일（transpile）
        - 폴리필은 브라우저가 지원하지 않는 코드를 브라우저에서 사용할 수 있도록 변환한 코드 조각이나，플러그인울 말한다. 트랜스파일은 최신 버전의 코드를 예전 버전의 코드로 변환하는 과정을 말한다. 둘 다 최신 기능을 구버전의 실행 환경에서 동작할 수 있게 바꿔주는 역할을 한다.
- 하지만 **언제까지나 이런 라이브러리에 기대어 크로스 브라우징 이슈를 해결할 수는 없었고 모든 브라우저에서 동일하게 동작하는 표준화된 자바스크립트의 필요성이 제기**되었다.
- **넷스케이프는 컴퓨터 시스템의 표준을 관리하는 Ecma 인터내셔널 （국제 표준화 기구）에 자바스크립트의 표준화를 위한 자바스크립트 기술 규격을 제출했고, Ecma 인터내셔널은 ECMAScript라는 이름으로 자바스크립트 표준화를 공식화**했다. 자바스크립트가 표준화되자 정적이던 웹사이트에서 동적인 웹 애플리케이션으로의 전환이 가속화되었다.

#### 1.1.3 웹사이트에서 웹 애플리케이션으로의 전환

<h5>웹사이트</h5>

- 중요한 것은 웹사이트는 수집된 데이터 및 정보를 특정 페이지에 표시하기 위한 정적인 웹이라는 것이다. 단방향으로 정보를 제공하기 때문에 사용자와 상호 작용하지 않으며, HTML에 링크가 연결된 웹 페이지 모음으로 콘텐츠가 동적으로 업데이트되지 않는다.

<h5>웹 애플리케이션</h5>

- 웹 애플리케이션은 사용자와 상호작용하는 쌍방향 소통의 웹을 말한다. 현재 우리가 접하는 대부분의 웹사이트는 웹 애플리케이션이지만, 본격적으로 웹 애플리케이션 시대의 서막을 연 서비스 중 하나가 구글 지도(Google Maps)였다. 기존의 지도 서비스는 이미 만들어져 있는 지도를 브라우저가 다운로드해서 보여주는 형태였는데, 구글 지도는 길을 찾기 위해 사용자가 직접 출발지와 도착지를 입력할 수 있다는 점에서 쌍방향 소통이 가능한 웹 애플리케이션이다.
- 이러한 웹의 성장은 개발자가 구현해야 하는 결과물 규모도 이전에 비해 커졌다는 의미이기 도 하다. 하지만 기존의 웹 개발 환경은 단순 정보 모음집 수준의 웹사이트 개발에 맞춰져 있 어서, 수많은 애플리케이션이 동작하는 대규모 웹 애플리케이션을 만드는 데는 적합하지 않았다. 개발 규모가 커진 만큼 개발 생태계도 이런 흐름에 맞춘 변화가 필요했다.

#### 1.1.4 개발 생태계의 발전

- 거대한 웹 애플리케이션이 등장하면서 개발 환경은 어떻게 변화했을까? 대규모 웹 서비스 개발의 필요성이 커지면서 하나의 웹 페이지를 통으로 개발하는 것이 아니라, 컴포넌트 단위로 개발하는 방식이 생겨났다.
- 웹 서비스는 점차 페이지에서 애플리케이션의 특성을 가지게 되었다. 서비스 규모가 커짐에 따라 다뤄야 하는 데이터가 폭발적으로 늘어났고, 표현해야 하는 화면도 다양하고 복잡해졌다. 요즘은 수많은 디바이스가 웹과 연결되어 있으며, 웹을 표현하는 디스플레이도 모바일, 패드, 노트북, 랩톱 PC 모니터 등 매우 다양하다. 사용자는 저마다의 디바이스에 최적화된 UX/UI를 기대한다.
- 이런 상황과 맞물려 앞서 언급한 컴포넌트 베이스 개발Component Based Development（CBD）방법론이 등장했다. CBD는 서비스에서 다루는 데이터를 구분하고 그에 맞는 UI를 표현할 수 있게 컴포넌트 단위로 개발하는 접근 방식이다. 요즘은 작은 컴포넌트를 조합해서 좀 더 큰 컴포넌트를 만들어가는 방식이 주류가 되었다.
- 컴포넌트는 모듈과 유사하게 하나의 독립된 기능을 재사용하기 위한 묶음이다. 다만 모듈과는 달리 런타임 환경에서 독립적으로 배포 - 실행될 수 있는 단위이다.
- 작은 기능을 가진 컴포넌트일수록 다른 컴포넌트에 의존하지 않고 독립적으로 존재할 수 있지만, 조합 과정에서 필연적으로 의존성이 생긴다. 따라서 개발자는 컴포넌트 간의 의존성을 파악해야 제대로 컴포넌트를 사용하고 변화에 대응할수 있게 된다.
- 이런 개발 생태계의 발전과 거대한 동적 웹 서비스의 수요 증가는 자연스럽게 자바스크립트 개발자의 증가로 이어졌다

#### 1.1.5 개발자 협업믜 필요섬 증가

- 결과물이 커졌기 때문에 서비스를 개발하고 나서 유지보수를 하는 데 협업의 중요성도 높아졌다. 개발에 투입된 인원이 많아질수록 코드를 파악하기 어려워지는 것은 당연지사다. 따라서 거대한 프로덕트를 개발할 때 효과적인 유지보수를 위한 협업 보완책이 필요하게 되었다. 대규모 프로덕트를 개발하고 나서 자바스크립트는 과연 유지보수에 적합한 언어 였을까?

### 1.2 자바스크립트의 한계

#### 1.2.1 동적 타입 언어

- **자바스크립트 특징 중 하나가 동적 타입 언어라는 것이다.** 이 말은 변수에 타입을 명시적으로 지정하지 않고 코드가 실행되는 런타임에 변숫값이 할당될 때 해당 값의 타입에 따라 변수 타입이 결정된다는 것을 의미한다.

#### 1.2.2 동적 타이핑 시스템의 한계

- 앞에서 설명한 내용은 자바스크립트를 사용하는 개발자, 즉 사람의 한계라고 바꿔 말할 수 있다.

```js
// 이 함수는 숫자 a, b의 합을 반환한다
const sumNumber = (a, b) => {
    return a + b;
}；
sumNumber(100); // NaN
sumNumberC("a", "b") // ab
```

- 어떤 에러도 발생하지 않고 정상적으로 동작한다. 따라서 여전히 정상적인 코드다. 동의하는가? 아마도 주석과 함수 이름 때문에 고개를 갸웃거리게 될 것이다.
- 자바스크립트 엔진은 이 코드를 문제 없이 실행한다. 왜나하면 **자바스크립트는 동적 타입 언어 이자 상당히 관대한 언어이기 때문이다. 동적 타입 언어라는 특성 때문에 sumNumber 함수를 호출할 때 사용되는 인수 값에 따라 a와 b의 타입이 결정된다.**
- 자바스크립트의 관대함은 이뿐만이 아니다. b가 undefined이기 때문에 + 연산자의 피연산
자가 될 수 없지만 오류를 발생시키지 않고, b를 적절한 타입인 NaN으로 형변환한 다음 실행
을 이어 나간다. 따라서 이 코드는 기계 입장에서는 정상적이지만 사람 입장에서는 정상적이지 않은 코드이다. 자바스크립트는 이 문제를 어떻게 해결하려 했을까?

#### 1.2.3 한계 극복을뮈한 해결밤안

- 거대한 웹 서비스를 개발하는 데 수많은 개발자의 협업이 필요한 상황에서 위와 같은 동적 타이핑 시스템의 한계는 상당한 어려움을 야기했다. 따라서 개발자들은 자바스크립트 인터페이스의 필요성을 느끼게 되었고 JSDoc, propTypes, 다트 같은 해결 방안이 등장했다.

<h5>JSDoc</h5>

- JSDoc은 모듈, 네임스페이스, 클래스, 메서드, 매개변수 등에 대한 API 문서 생성 도구다. 주석에 @ts- check를 추가하면 타입 및 에러 확인이 가능하며 자바스크립트 소스코드에 타입 힌트를 제공하는 HTML 문서를 생성할 수 있다. 하지만 어디까지나 주석의 성격을 지니고 있기 때문에 강제성을 부여해 사용하기 어렵다는 단점이 있다.

<h5>propTypes</h5>

- propTypes는 리액트에서 컴포넌트 props의 타입을 검사하기 위해 사용하는 속성이다. prop에 유효한 값이 전달되었는지 확인할 수 있지만, 전체 애플리케이션의 타입 검사를 하는데는 사용할 수 없다. 또한 리액트라는 특정 라이브러리에서만 사용할 수 있다는 점에서 한계가 있다.

#### 1.2.4 타입스크립트의 등장

- 시간이 지나 마이크로소프트는 자바스크립트의 슈퍼셋 언어인 타입스크립트를 공개했다. 다트와 달리 자바스크립트 코드를 그대로 사용할 수 있었고, 아래와 같은 단점을 극복할 수 있었기 때문에 많은 환영을 받았다.

<h5>안정성 보장</h5>

- 타입스크립트는 정적 타이핑을 제공한다. 컴파일 단계에서 타입 검사를 해주기 때문에 자바스크립트를 사용했을 때 빈번하게 발생하는 타입 에러를 줄일 수 있고, 런타임 에러를 사전에 방지할수 있어 안정성이 크게 높아진다.

<h5>개발 생산성 향상</h5>

- VSCode 등의 IDE에서 타입 자동 완성 기능을 제공한다. 이 기능으로 **변수와 함수 타입을 추론**할 수 있고, **리액트를 사용할 때 어떤 prop을 넘겨야 하는지 매번 확인하지 않아도 사용부에서 바로 볼 수 있기 때문에 개발 생산성이 크게 향상**된다.

<h5>협업에 유리</h5>

- 타입스크립트를 사용하면 복잡한 애플리케이션 개발 협업에 유리하다. 타입스크립트는 인터페이스interface, 제네릭Generic 등을 지원흐）는데 인터페이스가 기술되면 코드를 더 쉽게 이해할수 있게 도와준다. 또한 복잡한 애플리케이션일수록 협업하는 개발자 수도 증가하는데 자동 완성 기능이나 기술된 인터페이스로 코드를 쉽게 파악할 수 있다.

<h5>자바스크립트에 점진적으로 적용 가능</h5>

- 타입스크립트는 자바스크립트의 슈퍼셋이기 때문에 일괄 전환이 아닌 점진적 도입이 가능하다. 전체 프로젝트가 아닌 일부 프로젝트, 그중에서도 일부 기능부터 점진적으로 도입해볼 수 있다.


<h3>끝!</h3>
