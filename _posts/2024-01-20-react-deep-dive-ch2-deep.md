---
title: 2장 리액트 핵심 요소 깊게 살펴보기
categories:
- React Deep Dive
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

이번 장에서는 리액트를 이루는 핵심적인 개념을 깊게 샆펴보고, 이러한 기능이 자바스크립트를 토대로 어떻게 동작하는지 알아보자.

#### 2.1 JSX란?

- 보통 리액트를 통해 JSX를 접하기 때문에 JSX가 리액트의 전유물이라고 오해하는 경우가 종종 있다. 페이스북에서 독자적으로 개발했다는 사실에서 미루어 알 수 있듯이 JSX는 이른바 ECMAScript라고 불리는 자바스크립트 표준의 일부는 아니다. 즉, V8이나 Deno와 같은 자바스크립트 엔진이나 크롬, 웨일, 파이어폭스 같은 브라우저에 의해서 실행되거나 표현되도록 만들어진 구문이 아니다.
- 앞서 언급했던 것처럼 JSX는 자바스크립트 표준 코드가 아닌 페이스북이 임의로 만든 새로운 문법이기 때문에 JSX는 반드시 트랜스파일러를 거쳐야 비로소 자바스크립트 런타임이 이해할 수 있는 의미 있는 자바스크립트 코드로 변환된다.
- JSX의 설계 목적은 JSX 내부에 트리 구조로 표현하고 싶은 다양한 것들을 작성해두고, 이 JSX를 트랜스파일이라는 과정을 거쳐 자바스크립트(ECMAScript)가 이해할 수 있는 코드로 변경하는 것이 목표라고 볼 수 있다.
- **요약하자면 JSX는 자바스크립트 내부에서 표현하기 까다로웠던 XML 스타일의 트리 구문을 작성하는 데 많은 도움을 주는 새로운 문법이라고 볼 수 있다.** 이제 본격적으로 JSX가 어떻게 구성돼 있고 자바스크립트가 이를 이해하기 위해 어떤 과정을 거쳐야 하는지 살펴보자.

#### 2.1.1 JSX의 정의

- JSX는 기본적으로 JSXEIement, JSXAttributes, JSXChildren, JSXStrings라는 4가지 컴포넌트를 기반으로 구성돼 있다. 각 컴포넌트에 대해 살펴보자.

<h5>JSXElement</h5>

- JSX를 구성하는 가장 기본 요소로, HTML의 요소(element)와 비슷한 역할을 한다. JSXEIement가 되기 위해서는 다음과 같은 형태 중 하나여야 한다.
    - - JSXOpeningElenient: 일반적으로 볼 수 있는 요소다. JSXOpeningElement로 시작했다면 후술할 JSXClosingElement
가 동일한 요소로 같은 단계에서 선언돼 있어야 올바른 JSX 문법으로 간주된다.
    - JSXClosingElement: JSXOpeningElement가 종료됐음을 알리는 우소루, 반드시 JSXOpeningElement와 쌍으로 사용돼야 한다.
    - JSXSelfClosingElement: 요소가 시작되고, 스스로 종료되는 형태를 의미한다. <script/>와 동일한 모습을 띠고 있다. 이는 내부적으로 자식을 포함할 수 없는 형태를 의미한다.
    - JSXFragment: 아무런 요소가 없는 형태로, JSXSelfClosingElement 형태를 띨 수는 없다. "</>"는 불가능하다. 단 "<></>" 는가능하다.

<h5>JSXAttributes</h5>

- JSXEIement에 부여할 수 있는 속성을 의미한다. 단순히 속성을 의미하기 때문에 모든 경우에서 필수값이 아니고, 존재하지 않아도 에러가 나지 않는다.

    - JSXSpreadAttributes: 자바스크립트의 전개 연산자와 동일한 역할을 한다고 볼 수 있다.
        - {.. .AssignmentExpression}: 이 AssignmentExpression에는 단순히 객체뿐만 아니라 자바스크립트에서 AssignmentExpression으로 취급되는 모든 표현식이 존재할 수 있다. 여기에는 조건문 표현식, 화살표 함수, 할당식 등 다양한 것이 포함돼 있다.
    - JSXAttribute： 속성을 나타내는 키와 값으로 짝을 이루어서 표현한다. 키는 JSXAttributeName, 값은 JSXAttribute Value로 불린다.

<h5>JSXChildren</h5>

- JSXEIement의 자식 값을 나타낸다. JSX는 앞서 언급했듯 속성을 가진 트리 구조를 나타내기 위해 만들어졌기 때문에 JSX로 부모와 자식 관계를 나타낼 수 있으며, 그 자식을 JSXChildren이라고 한다.

<h5>JSXStrings</h5>

- HTML에서 사용 가능한 문자열은 모두 JSXStrings에서도 가능하다. 이는 개발자가 HTML의 내용을 손쉽게 JSX로 가져올 수 있도록 의도적으로 설계된 부분이다. 여기서 정의된 문자열이라 함은, "큰따옴표로 구성된 문자열", '작은따옴표로 구성된 문자열' 혹은 JSXText를 의미한다.

#### 2.1.2 JSX 예제

- 백문불여일견!! 코드를 보며, 앞서 소개한 4가지 요소를 조합해 JSX를 만들어 보자. 다음 예제는 모두 유효한JSX 구조를 띠고 있다.

```js
// 하나의 요소로 구성된 가장 단순한 형태
const ComponentA = <A>안녕하세요.</A>
// 자식이 없이 SelfClosingTag로 닫혀 있는 형태도 가능하다.
const ComponentB = <A />
// 옵션을 { }와 전개 연산자로 넣을 수 있다.
const ComponentC = <A {...{ required: true }} />
// 속성만 넣어도 가능하다.
const ComponentO = <A required />
// 속성과 속성을 넣을 수 있다.
const ComponentE = <A required={false} />
const ComponentF = (
    <A>
        {/* 문자열은 큰따옴표 및 작은따옴표 모두 가능하다. */}
        <B text="리액트" />
    </A>
)
const ComponentG = (
    <A>
        {/* 옵션의 값으로 JSXEIement를 넣는 것 또한 올바론 문법이다. */}
        <B optionalChildren={<>안녕하세요.</>} />
    </A>
)
const ComponentH = (
    <A>
        {/* 여러 개의 자식도 포함할 수 있다. */}
        안녕하세요
        <B text="리액트" />
    </A>
)
```

#### 2.1.3 JSX는 어떻게 자바스크립트로 변환될까?

- 우선 자바스크립트에서 JSX가 변환되는 방식을 알려면 **리액트에서 JSX를 변환하는 @babel/plugintransform-react-jsx 플러그인을 알아야 한다. 이 플러그인은 JSX 구문을 자바스크립트가 이해할 수 있는 형태로 변환**한다.
- 다음과 같은JSX 코드가 있다고 가정해 보자.

```js
const ComponentA = <A r은quired={tr니e}>Hello World</A>
const ComponentB = oHello World</>
const ComponentC = (
    <div>
        <span>hello world</span>
    </div>
)
```

- 이를 변환한 결과는 다음과 같다.

```js
var ComponentA = React.createElement(
    A,
    {
    required: true,
    },
    'Hello World',
)
var ComponentB = React.createElement(React.Fragment, null, 'Hello World')
var ComponentC = React.createElement(
    'div',
    null.
    React.createElement('span', null, 'heUo world'),
)
```

- @babel/plugin-transform-react-jsx를 직접 써보고 싶다면 필요한 패키지를 설치하고 다음과 같이 코드를 작성하면 된다.

#### 2.1.4 정리

- JSX는 자바스크립트 코드 내부에 HTML과 같은 트리 구조를 가진 컴포넌트를 표현할 수 있다는 점에서 각광받고 있다. 물론 인기만 있는 것은 아니다. JSX가 HTML 문법과 자바스크립트 문법이 뒤섞여서 코드 가독성을 해친다는 의견도 있다. JSX 내부에 자바스크립트 문법이 많아질수록 복잡성이 증대하면서 코드의 가독성도 해칠 것이므로 주의해서 사용해야 한다.

#### 2.2 가상 DOM과 리액트 파이버

- 리액트의 특징으로 가장 많이 언급되는 것 중 하나는 바로 실제 DOM이 아닌 가상 DOM을 운영한다는 것이다. 그러나 가상 DOM이 왜 만들어졌는지, 실제 DOM과는 어떤 차이가 있는지, 그리고 정말로 실제 DOM 을 조작하는 것보다 빠른지에 대해서는 잘 모르는 경우가 많다. 이번 장에서는 리액트의 가상 DOM이 무엇인지, 그리고 실제 DOM에 비해 어떤 이점이 있는지 살펴보고 가상 DOM을 다룰 때 주의할 점에 대해서도 알아보겠다.

#### 2.2.2 가상 DOM의 탄생 배경

- 가상 DOM은 말 그대로 실제 브라우저의 DOM이 아닌 리액트가 관리하는 가상의 DOM을 의미한다. 가상 DOM은 웹페이지가 표시해야 할 DOM을 일단 메모리에 저장하고 리액트가 실제 변경에 대한 준비가 완료됐을 때 실제 브라우저의 DOM에 반영한다. 이렇게 DOM 계산을 브라우저가 아닌 메모리에서 계산하는 과정을 한 번 거치게 된다면 실제로는 여러 번 발생했을 렌더링 과정을 최소화할 수 있고 브라우저와 개발자의 부담을 덜 수 있다.

#### 2.2.3 가상 DOM을 위한 아키텍처, 리액트 파이버

- 그렇다면 이러한 가상 DOM을 만드는 과정을 리액트는 어떻게 처리하고 있을까? 리액트는 여러 번의 렌더링 과정을 압축해 어떻게 최소한의 렌더링 단위를 만들어 내는 것일까? 이러한 **가상 DOM과 렌더링 과정 최적화를 가능하게 해주는 것이 바로 리액트 파이버(React Fiber)다.**

<h5>리액트 파이버란?</h5>

- **리액트 파이버는 리액트에서 관리하는 평범한 자바스크립트 객체**다. 파이버는 파이버 재조정자(fiber reconciler)가 관리하는데, 이는 앞서 이야기한 가상 DOM과 실제 DOM을 비교해 변경 사항을 수집하며, 만약 이 둘 사이에 차이가 있으면 **변경에 관련된 정보를 가지고 있는 파이버를 기준으로 화면에 렌더링을 요청하는 역할**을 한다.
- 리액트 파이버의 목표는 리액트 웹 애플리케이션에서 발생하는 애니메이션, 레이아웃, 그리고 사용자 인터랙션에 올바른 결과물을 만드는 반응성 문제를 해결하는 것이다. 이를 위해 파이버는 다음과 깉은 일을 할 수 있다.
    - 작업을 작은 단위로 분할하고 쪼갠 다음, 우선순위를 매긴다.
    - 이러한 작업을 일시 중지하고 나중에 다시 시작할 수 있다.
    - 이전에 했던 작업을 다시 재사용하거나 필요하지 않은 경우에는 폐기할 수 있다.

- 한 가지 중요한 것은 이러한 모든 과정이 비동기로 일어난다는 것이다. **과거 리액트 리액트의 조정 알고리즘은 스택 알고리즘으로 이뤄져 있었다.** 스택이라는 이름에서 유추할 수 있듯이 과거에는 이 하나의 스택에 렌더링에 필요한 작업들이 쌓이면 이 스택이 빌 때까지 동기적으로 작업이 이루어졌다.
- 사용자 인터랙션에 따른 동시 다발적인 이벤트와 애니메이션은 다양한 작업을 처리하는 요즘의 웹 애플리케이션에서는 피할 수 없는 문제다. 이러한 **기존 렌더링 스택의 비효율성을 타파하기 위해 리액트 팀은 이 스택 조정자 대신 파이버라는 개념을 탄생시킨다.**
- **파이버는 일단 하나의 작업 단위로 구성돼 있다.** 리액트는 이러한 작업 단위를 하나씩 처리하고 finishedWork()라는 작업으로 마무리한다. 그리고 이 작업을 커밋해 실제 브라우저 DOM에 가시적인 변경 사항을 만들어 낸다. 그리고 이러한 단계는 아래 두 단계로 나눌 수 있다.
    - 1) **렌더 단계에서 리액트는 사용자에게 노출되지 않는 모든 비동기 작업을 수행**한다. 그리고 이 단계에서 앞서 언급한 파이버의 작업, 우선순위를 지정하거나 중지시키거나 버리는 등의 작업이 일어난다.
    - 2) **커밋 단계에서는 앞서 언급한 것처럼 DOM에 실제 변경 사항을 반영하기 위한 작업, commitWork()가 실행되는데. 이 과정은 앞서와 다르게 동기식으로 일어나고 중단될 수도 없다.**

- 파이버가 실제 리액트 코드에서 어떻게 구현돼 있는지 살펴보자.

```js
function FiberNode(tag, pendingProps, key, mode) {
    // Instance
    this.tag = tag
    this.key = key
    this.elementType = null
    this.type = null
    this.stateNode = null
    // Fiber
    this.return = null
    this.child = null
    this.sibling = null
    this.index = 0
    this.ref = null
    this, refCleanup = null
    this.pendingProps = pendingProps
    this.memoizedProps = null
    this.updateQueue = null
    this.memoizedState = null
    this.dependencies = null
    this.mode = mode
    // Effects
    this.flags = NoFlags
    this.subtreeFlags = NoFlags
    this.deletions = null
    this.lanes = NoLanes
    this.childLanes = NoLanes
    this.alternate = null
    // 이하 프로파일러, _DEV__ 코드는 생략
}
```

- 보다시피 파이버가 단순한 자바스크립트 객체로 구성돼 있는 것을 볼 수 있다. 파이버는 리액트 요소와 유사하다고 느낄 수 있지만 한 가지 중요한 차이점은 **리액트 요소는 렌더링이 발생 할 때마다 새롭게 생성되지만 파이버는 가급적이면 재사용된다는 사실**이다. 파이버는 컴포넌트가 최초로 마 운트되는 시점에 생성되어 이후에는 가급적이면 재사용된다.

- 리액트에 작성되어 있는 파이버를 생성하는 다양한 함수를 살펴보자.

```js
var createFiber = function (tag, pendingProps, key, mode) {
    return new FiberNode(tag, pendingProps, key, mode)
}
// 생략...
function createFiberFromElement(element, mode, lanes) {
    var owner = null
    {
        owner = element._owner
    }

    var type = element.type
    var key = element.key
    var pendingProps = element.props
    var fiber = createFiberFromTypeAndProps(
    type,
    key,
    pendingProps,
    owner,
    mode,
    lanes,
    )
        {
            fiber._debugSource = element._source
            fiber._debugOwner = element._owner
        }
        return fiber
    }
```

    - 리액트 파이버의 구현체를 봤으니 이제 여기서 선언된 주요 속성을 살펴보면서 어떠한 내용을 담고 있는지살펴보자.

    - tag: 파이버를 만드는 함수 이름인 createFiberFromElement를 보면 유추할 수 있겠지만 파이버는 하나의 element에 하나가 생성되는 1：1 관계를 가지고 있다.
    - stateNode： 이 속성에서는 파이버 자체에 대한 참조(reference) 정보를 가지고 있으며, 이 참조를 바탕으로 리액트는 파이버와 관련된 상태에 접근한다.
    - - child, sibling, return： 파이버 간의 관계 개념을 나타내는 속성이다. 리액트 컴포넌트 트리가 형성되는 것과 동일하게 파이버도 트리 형식을 갖게 되는데, 이 트리 형식을 구성하는 데 필요한 정보가 이 속성 내부에 정의된다. 한 가지 리액트 컴포넌트 트리와 다른 점은 children이 없다는 것, 즉 하나의 child만 존재한다는 점이다.
    - index： 여러 형제들(sibling) 사이에서 자신의 위치가 몇 번째인지 숫자로 표현한다.
    - pendingProps： 아직 작업을 미처 처리하지 못한 props
    - memoizedProps: pendingProps를 기준으로 렌더링이 완료된 이후에 pendingProps를 memoizedProps로 저장해 관리한다.
    - updateQueue: 상태 업데이트, 콜백 함수, DOM 업데이트 등 필요한 작업을 담아두는 큐. 이 큐는 대략 다음과 같은 구조를가지고 있다.
    - memoizedState： 함수형 컴포넌트의 훅 목록이 저장된다. 여기에는 단순히 useState뿐만 아니라 모든 훅 리스트가 저장된다.
    - alternate: 뒤이어 설명할 리액트 파이버 트리와 이어질 개념. 리액트의 트리는 두 개인데. 이 alternate는 반대편 트리파이버를 가리킨다.

- 생성된 파이버는 state가 변경되거나 생명주기 메서드가 실행되거나 DOM의 변경이 필요한 시점 등에 실행된다. 그리고 중요한 것은 리액트가 파이버를 처리할 때마다 이러한 작업을 직접 바로 처리하기도 하고 스케줄링하기도 한다는 것이다. 즉, 이러한 작업들은 작은 단위로 나눠서 처리할 수도, 애니메이션과 같이 우선순위가 높은 작업은 가능한 한 빠르게 처리하거나, 낮은 작업을 연기시키는 등 좀 더 유연하게 처리된다.
- 리액트 파이버의 가상 DOM이 생각보다 단순한 자바스크립트 객체로 관리되고 있다는 사실에 놀랄 수도 있다. 리액트 개발 팀은 사실 **리액트는 가상 DOM이 아닌 Value UI, 즉 값을 가지고 있는 UI를 관리하는 라이브러리라**는 내용을 피력한 바 있다.  파이버의 객체 값에서도 알 수 있듯이 **리액트의 핵심 원칙은 UI를 문자열, 숫자, 배열과 같은 값으로 관리한다는 것**이다. 변수에 이러한 UI 관련 값을 보관하고, 리액트의 자바스크립트 코드 흐름에 따라 이를 관리하고, 표현하는 것이 바로 리액트다.

<h5>리액트 파이버 트리</h5>

- 파이버의 개념에 대해 알아봤으니 그다음으로는 파이버 트리에 대해 알아보자. **파이버 트리는 사실 리액트 내부에서 두 개가 존재한다. 하나는 현재 모습을 담은 파이버 트리이고, 다른 하나는 작업 중인 상태를 나타내는 workInProgress 트리다.** 리액트 파이버의 작업이 끝나면 리액트는 단순히 포인터만 변경해 worklnProgress 트리를 현재 트리로 바꿔버린다. 이러한 기술을 더블 버퍼링이라고 한다.

<div><img src= "/assets/img/post/fiber_tree.png"></div>

- 즉, **먼저 현재 UI 렌더링을 위해 존재하는 트리인 current를 기준으로 모든 작업이 시작**된다. 여기에서 **만약 업데이트가 발생하면 파이버는 리액트에서 새로 받은 데이터로 새로운 worklnProgress 트리를 빌드하기 시작**한다. 이 worklnProgress 트리를 빌드하는 작업이 끝나면 다음 렌더링에 이 트리를 사용한다. 그리고 **이 worklnProgress 트리가 이에 최종적으로 렌더링되어 반영이 완료되면 current가 이 worklnProgress로 변경된다.**

<h5>파이버의 작업 순서</h5>

- 파이버와 파이버 트리에 대해 알아봤으니 이제 파이버 트리와 파이버가 어떤 식으로 작동하는지 흐름을 살펴보자. 먼저 일반적인 파이버 노드의 생성 흐름은 다음과 같다.

    1)  리액트는 beginWork() 함수를 실행해 파이버 작업을 수행하는데, 더 이상 자식이 없는 파이버를 만날 때까지 트리 형식으로 시작된다.
    2)  1 번에서 작업이 끝난다면 그다음 completeWorkO 함수를 실행해 파이버 작업을 완료한다.
    3) 형제가 있다면 형제로 넘어간다.
    4) 2번, 3번이 모두 끝났다면 return으로 돌아가 자신의 작업이 완료됐음을 알린다.

- 이렇게 트리가 생성됐다. **이제 여기서 setstate 등으로 업데이트가 발생하면 어떻게 될까?**
-  이미 리액트는 앞서 만든 current 트리가 존재하고, setState로 인한 업데이트 요청을 받아 worklnProgress 트리를 다시 빌드하기 시작한다. 이 빌드 과정은 앞서 트리를 만드는 과정과 동일하다. 최초 렌더링 시에는 모든 파이버를 새롭게 만들어야 했지만 이제는 파이버가 이미 존재하므로 되도록 새로 생성하지 않고 기존 파이버에서 업데이트된 props를 받아 파이버 내부에서 처리한다.

#### 2.2.4 파이버와 가상 DOM

- 앞서 언급했듯이 **리액트 컴포넌트에 대한 정보를 1：1로 가지고 있는 것이 파이버**이며, 이 파이버는 리액트 아키텍처 내부에서 비동기로 이뤄진다. 이러한 비동기 작업과 달리, 실제 브라우저 구조인 DOM에 반영하는 것은 동기적으로 일어나야 하고, 또 처리하는 작업이 많아 화면에 불완전하게 표시될 수 있는 가능성이 높으므로 이러한 작업을 가상에서, 즉 메모리상에서 먼저 수행해서 최종적인 결과물만 실제 브라우저 DOM에 적용하는 것이다.

#### 2.2.5 정리

- 지금까지 리액트에서 가상 DOM이라는 개념이 무엇인지, 또 가상 DOM을 구현하기 위해 만들어진 리액트 파이버의 개념과 이를 조정하는 재조정자에 대해서 알아봤다. 
- **가상 DOM과 파이버는 단순히 브라우저에 DOM을 변경하는 작업보다 빠르다는 이유로만 만들어진 것은 아니다.** 만약 이러한 도움 없이 개발자가 직접 DOM을 수동으로 하나하나 변경해야 한다면 어떤 값이 바뀌었는지, 또 그 값에 따라 어떠한 값이 변경됐고 이와 관련된 것들이 무엇이었는지 파악하기가 매우 어려울 것이다. 이러한 어려움을 리액트 내부의 파이버와
재조정자가 내부적인 알고리즘을 통해 관리해 줌으로써 대규모 웹 애플리케이션을 효율적으로 유지보수하고 관리할 수 있게 된 것이다.
- 가상 DOM과 리액트의 핵심은 브라우저의 DOM을 더욱 빠르게 그리고 반영하는 것이 아니라 바로 **값으로 UI를 표현하는 것**이다. 화면에 표시되는 UI를 자바스크립트의 문자열, 배열 등과 마찬가지로 값으로 관리하고 이러한 흐름을 효율적으로 관리하기 위한 메커니즘이 바로 리액트의 핵심이다.

#### 2.3 클래스형 컴포넌트와 함수형 컴포넌트

- 함수형 컴포넌트가 각광받기 시작한 것은 16.8 버전 에서 훅이 소개된 이후였다. 함수형 컴포넌트에 훅이 등장한 이후 함수형 컴포넌트에서 상태나 생명주기 메서드 비슷한 작업을 흉내 낼수 있게 되자 상대적으로 보일러플레이트가 복잡한 클래스형 컴포넌트보다 함수형 컴포넌트를 더 많이 쓰기 시작했다.





<h3>끝!</h3>
