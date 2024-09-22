---
title: 8장 JSX에서 TSX로
categories:
- 우아한 타입스크립트 with 리액트
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/elegant_typescript.png"
---

리액트 애플리케이션을 타입스크립트로 작성할 때 @types/react 패키지에 정의된 리액트 내장 타입을 사용해본 경험이 있을 것이다. 리액트 내장 타입 중에는 역할이 명확한 것도 있지만, 역할이 비슷해 보이는 타입도 존재하기 때문에 어떤 것을 사용해야 할지 헷갈릴 때가 있
다. 이 절에서는 헷갈릴 수 있는 대표적인 리액트 컴포넌트 타입을 살펴보면서 상황에 따라 어떤 것을 사용하면 좋을지 그리고 사용할 때의 유의점은 무엇인지 알아보자.


### 8.2 함수 컴포넌트 타입

```js
interface WelcomeProps {
    name： string；
}

class Welcome extends React.Component<WelcomeProps> {
/* ... 생략 */
}

// 함수 선언을 사용한 방식
function Welcome (props： WelcomeProps)： JSX.Element {}

// 함수 표현식을 사용한 방식 - React.FC 사용
const Welcome： React.FC<WelcomeProps> = ({ name }) => {}；

// 함수 표현식을 사용한 방식 - React.VFC 사용
const Welcome： React.VFC<WelcomeProps> = ({ name }) => {};

// 함수 표현식을 사용한 방식 - JSX.Element를 반환 타입으로 지정
const Welcome = ({ name }： WelcomeProps)： JSX.Element => {};

type FC<P = {}> = FunctionComponent<P>;

interface FunctionConiponent<P = {}> {
    // props에 children을 추가
    (props： PropsWithChildren<P>, context?： any)： ReactElementony, any> | null;
    propTypes?: WeakValiclationMap<P> | undefined;
    contextTypes?： ValidationMap<any> | undefined;
    defaultProps?: Partial<P> | undefined;
    displayName?： string | undefined;
}

type VFC<P = {}> = VoidFunctionComponent<P>;

interface VoidFunctionComponent<P = {}> {
    // children 없음
    (props： P, context?; any): ReactElementony, any> | null;
    propTypes?: WeakValidationMap<P> | undefined;
    contextTypes?： ValidationMap<any> | undefined;
    defaultprops?： Partial<P> | undefined;
    displayName?： string | undefined;
}
```

- 함수 표현식을 사용하여 함수 컴포넌트를 선언할 때 가장 많이 볼 수 있는 형태는 React.FC 혹은 React.VFC로 타입을 지정하는 것이다. FC는 Functioncomponent의 약자로 **React.FC와 React.VFC는 리액트에서 함수 컴포넌트의 타입 지정을 위해 제공되는 타입**이다.
- 먼저 React.FC가등장하고 이후@types/react 16.9.4버전에서 React.VFC 타입이 추가되었다. 둘 사이에는 children이라는 타입을 허용하는지 아닌지에 따른 차이를 보인다.
- 하지만 리액트 V18로 넘어오면서 React.VFC가삭제되고 React.FC에서 children이 사라졌다. 그래서 앞으로는 React.VFC 대신 React.FC 또는 props 타입 - 반환 타입을 직접 지정하는 형태로 타이핑해줘야 한다.

### 8.3 Children props 타입 지정

```js
type PropsWithChildren<P> = P & { children?： ReactNode | undefined };
type ReactNode = ReactElement | string | number | boolean | null | undefined | ReactNode[];
```

- 
ReactNode는 TypeScript에서 React 컴포넌트가 렌더링할 수 있는 모든 유형의 값을 표현하는 타입이다. 이는 React 컴포넌트의 반환값이 될 수 있는 다양한 값들을 포괄적으로 나타내며, 자식 요소로 무엇이든 받을 수 있게 해준다.
- 가장 보편적인 children 타입은 ReactNode | undefined가 된다. ReactNode는 ReactElement 외에도 boolean, number 등 여러 타입을 포함하고 있는 타입으로, 더 구체적으로 타이핑하는 용도에는 적합하지 않다. 예를 들어 특정 문자열만 허용하고 싶을 때는 children에 대해 추가로 타이핑해줘야 한다. 

```js
// example 1
type WelcomeProps = {
    children: "천생연분" | "더 귀한 분" | "귀한 분" | "고마운 분";
}；
// example 2
type WelcomeProps = {
    children: string;
}；
// example 3
type WelcomeProps = {
    children： ReactElement;
};
```

### 8.4 render 메서드와 함수 컴포넌트의 반환 타입 - React.ReactElement vs JSX.Element vs ReactReactNode

- React.ReactElement, JSX.Element. React.ReactNode 타입은 쉽게 헷갈릴 수 있기 때문에 자세히 살펴보자. 함수 컴포넌트의 반환 타입인 ReactElement는 아래와 같이 정의된다.
- **ReactElement는 React가 JSX 코드를 변환한 후 반환하는 JavaScript 객체다. 가상 DOM에서 사용**되며, 실제 DOM과는 분리된 중간 데이터 구조다.

```js
interface ReactElement<P = any,
    T extends string | JSXElementConstructor<any> =
    | string
    | JSXElementConstructor<any>
> {
    type： T;
    props： P;
    key: Key | null;
}
```

- **React.createElement를 호출하는 형태의 구문으로 변환하면 React.createElement의 반환 타입은 ReactElement**이다. 리액트는 실제 DOM이 아니라 가상의 DOM을 기반으로 렌더링하는데 가상 DOM의 엘리먼트는 ReactElement 형태로 저장된다. 즉, **ReactElement 타입은 리액트 컴포넌트를 객체 형태로 저장하기 위한 포맷**이다.

```js
// JSX
const element = <div className="container">Hello, world!</div>;
// React.createElement 함수로 반환된 ReactElement
const element = {
  type: 'div',                        // 요소 타입 (HTML 태그 'div')
  props: {                            // 요소에 전달된 속성
    className: 'container',
    children: 'Hello, world!',
  },
  key: null,                          // 리스트에서 사용되는 고유한 키 (현재는 null)
  ref: null,                          // 참조 (ref) 값 (현재는 null)
};
```

```js
declare global {
    namespace JSX {
        interface Element extends React.ReactElement<any, any> {}
    }
}
```

- **JSX.Element 타입은 앞의 코드를 보면 알 수 있다시피 리액트의 ReactElement를 확장하고 있는 타입**이며, 글로벌 네임스페이스에 정의되어 있어 외부 라이브러리에서 컴포넌트 타입을 재정의 할 수 있는 유연성을 제공한다. 
- 이러한 특성으로 인해 컴포넌트 타입을 재정의하거나 변경하는것이 용이해진다. React.Node는 다음과 같이 타입이 정의되어 있다.

```js
type ReactText = string | number;
type ReactChild = ReactElement | ReactText;
type ReactFragment = {} | Iterable<ReactNode>;
type ReactNode =
    | ReactChild
    | ReactFragment
    | ReactPortal
    | boolean
    | null
    | undefined;
```

- 단순히 ReactElement 외에도 boolean, string, number 등의 여러 타입을 포함하고 있다. ReactNode, JSX.Element, ReactElement 사이의 포함관계를 정리하면 아래 그림과 같다.

<div><img src= "/assets/img/post/reactnode_jexelement_reactelement.PNG"></div>

### 8.5 ReactElement, ReactNode, JSX.Element 활용하기

- ReactElement, ReactNode, JSX.Element는 모두 리액트의 요소를 나타내는 타입이다. 리액트의 요소를 나타내는 데 왜 이렇게 많은 타입이 존재하는지 의문이 생길 수도 있을 것이다.
- 이 절에서는 이 **3가지 타입의 차이점과 어떤 상황에서 어떤 타입을 사용해야 더 좋은 코드를 작성할 수 있는지**를 소개한다.

#### 8.5.1 ReactElement

- 리액트 엘리먼트를 생성하는 createElement 메서드에 대해 들어본 적이 았을 것이다. 리액트를 사용하면서 JSX라는 자바스크립트를 확장한 문법을 자주 접했을 텐데 **JSX가 createElement 메서드를 호출하기 위한 문법**이다.

- JSX?
    - **JSX는 자바스크립트의 확장 문법으로 리액트에서 UI를 표현하는 데 사용**된다. HTML과 유사한 문법을 제공하여 리액트 사용자에게 렌더링 로직（마크업）을 쉽게 만들 수 있게 해주고, 컴포넌트 구조와 계충 구조를 편리하게 표현할 수 았도록 해준다.

- 즉, JSX는 리액트 엘리먼트를 생성하기 위한 문법이며 트랜스파일러는 JSX 문법을 createElement 메서드 호출문으로 변환하여 아래와 같이 리액트 엘리먼트를 생성한다.

```js
const element = React.createElement(
    "hl",
    { ClassName： "greeting" },
    "Hello, world!"
);

// 주의: 다음 구조는 단순화되었다
const element = {
        type： "hl",
    props： {
        className： "greeting",
        children： "Hello, world!",
    },
};

declare global {
    namespace JSX {
        interface Element extends React.ReactElement<any, any> {
            // ...
        }
        // ... 
    }
}
```
- 리액트는 이런 식으로 만들어진 리액트 엘리먼트 객체를 읽어서 DOM을 구성한다. 리액트에는 여러 개의 createElement 오버라이딩 메서드가 존재하는데, 이 메서드들이 반환하는 타입은 ReactElement 타입을 기반으로 한다.
- **정리하면 ReactElement 타입은 JSX의 createElement 메서드 호출로 생성된 리액트 엘리먼트를 나타내는 타입이라고 볼 수 있다.**

#### 8.5.2 ReactNode

- R은actChild 타입은 ReactElement | string | number로 정의되어 ReactElement보다는 좀 더 넓은 범위를 갖고 있다.
- ReactNode는 앞에서 설명한 ReactChild 외에도 boolean, null, undefined 등 훨씬 넓은 범주의 타입을 포함한다. 즉, ReactNode는 리액트의 render 함수가 반환할 수 있는 모든 형태를 담고 있다고 볼 수 있다

#### 8.5.3 JSX.Element

- JSX. Element는 ReactElement의 제네릭으로 props와 타입 필드에 대해 any 타입을 가지도록 확장하고 있다. 즉, JSX.Element는 ReactElement의 특정 타입으로 props와 타입 필드를 any로 가지는 타입이라는 것을 알 수 있다.

### 8.6 사용 예시

- 이렇게 ReactElement, ReactNode, JSX.Element 3가지 타입에 대해 자세히 살펴보았는데, **이들의 공통점은 모두 리액트에서 제공하는 컴포넌트를 나타낸다는 것**이다. 그러면 어떤 상황에 어떤 타입을 사용하는 게 좋을까? 예시를 통해 알아보자.

#### 8.6.1 ReactNode

```js
type ReactNode = ReactElement | string | number | boolean | null | undefined | ReactNode[];
```

- ReactNode 타입은 앞서 언급한 대로 리액트의 render 함수가 반환할 수 있는 모든 형태를 담고 있기 때문에 리액트 컴포넌트가 가질 수 있는 모든 타입을 의미한다.
- 리액트의 Composition(합성) 모델을 활용하기 위해 prop으로 children을 많이 사용해봤을 것이다. children을 포함하는 props 타입을 선언하면 다음과 같다.

```js
interface MyComponentProps {
    children?： React.ReactNode;
    // ...
}
```

- JSX 형태의 문법을 **때로는 string, number, null, undefined같이 어떤 타입이든 children prop으로 지정할 수 있게 하고 싶다면 ReactNode 타입으로 children을 선언**하면 된다.

#### 8.6.2 JSX.Element

- **JSX.Element는 앞서 언급한 대로 props와 타입 필드가 any 타입인 리액트 엘리먼트를 나타낸다. 이러한 특성 때문에 리액트 엘리먼트를 prop으로 전달받아 render props 패턴으로 컴포넌트를 구현할 때 유용하게 활용할 수 있다.** 다음 코드를 살펴보자.

```js
interface Props {
    icon： JSX.Element;
}

const Item = ({ icon }: Props) => {
    // prop으로 받은 컴포넌트의 props에 접근할 수 있다
    const iconsize = icon.props.size;
    return (<li>{icon}</ll>);
};

// icon prop에는 JSX.Element 타입을 가진 요소만 할당할 수 있다
const App = 0 => {
    return <Item icon={<Icon size={14} />} />
}；
```

- icon prop을 JSX.Element 타입으로 선언함으로써 해당 prop에는 JSX 문법만 삽입할 수 있다. 또한 icon.props에 접근하여 prop으로 넘겨받은 컴포넌트의 상세한 데이터를 가져올 수 있다.

#### 8.6.3 ReactElement

- 앞서 살펴본 JSX.Element 예시를 확장하여 추론 관점에서 더 유용하게 활용할 수 있는 방법은 JSX.Element 대신에 ReactElement을 사용하는 것이다.
- 이때 원하는 컴포넌트의 props를 ReactElement의 제네릭으로 지정해줄 수 있다. 만약 JSX.EIement가 ReactElement의 props 타입으로 any가 지정되었다면, ReactElement 타입을 활용하여 제네릭에 직접 해당 컴포넌트의 props 타입을 명시해준다.

```js
interface IconProps {
    size： number；
}
interface Props {
    // ReactElement의 props 타입으로 IconProps 타입 지정
    icon： React.ReactEleinent<IconProps>;
}

const Item = ({ icon }： Props) => {
    // icon prop으로 받은 컴포넌트의 props에 접근하면, props의 목록이 추론된다
    const iconsize = icon.props.size;
    return <li>{icon}</li>;
};
```

### 8.7 리액트에서 기본 HTML 요소 타입 활욤하기

- 리액트를 사용하면서 HTML button 태그를 확장한 Button 컴포넌트를 만들어본 경험이 있을 것이다.

```js
 const SquareButton = () => <button>정사각형 버튼</button>;
```

- 이렇게 새롭게 만든 Button 컴포넌트는 기존 HTML button과 같은 역할을 하면서도 새로운 기능이나 UI가 추가된 형태이다. 기존의 button 태그가 클릭 이벤트를 등록하기 위한 onClick 이벤트 핸들러를 지원하는 것처럼, 새롭게 만든 Button 컴포넌트도 onClick 이벤트 핸들러를 지원해야만 일관성과 편의성을 모두 챙길 수 있다. 이 절에서는 기존 HTML 태그의 속성 타입을 활용하여 타입을 지정하는 방법에 대해 알아보자.

#### 8.7.1 DetailedHTMLProps와 ComponentWithoutRef

- HTML 태그의 속성 타입을 활용하는 대표적인 2가지 방법은 리액트의 DetaiiedHTMLProps와 ComponentPropsWithoutRef 타입을 활용하는 것이다.
- 먼저 React.DetaiiedHTMLProps를 활용하는 경우에는 아래와 같이 쉽게 HTML 태그 속성과 호환되는 타입을 선언할 수 있다

```js
type NativeButtonProps =React.DetailedHTMLProps<
    React.ButtonHTMLAttributes<HTMLButtonElement>,
    HTMLButtonElement
>;

// ButtonProps의 onClick 타입은 실제 HTML button 태그의 onClick 이벤트 핸들러 타입과 동일
type ButtonProps = {
    onClick?： NativeButtonProps["onClick"];
}；
```

- 그리고 React. ComponentPropsWithoutRef 타입은 아래와 같이 활용할 수 있다.

```js
type NativeButtonType = React.ComponentPropsWithoutRef<"button">;
// 마찬가지로 리액트의 button onClick 이벤트 핸들러에 대한 타입이 할당
type ButtonProps = {
    onClick?： NativeButtonType["onClick"];
}；
```

<h3>끝!</h3>
