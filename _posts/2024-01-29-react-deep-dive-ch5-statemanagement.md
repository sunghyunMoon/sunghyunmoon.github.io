---
title: 5장 리액트와 상태 관리 라이브러리
categories:
- React Deep Dive
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

이번 장에서는 리액트 애플리케이션을 개발할 때 빠지지 않고 언급되는 상태 관리 라이브러리에 대해 알아본다. 많은 개발자들이 리액트 애플리케이션에 자신이 익숙한 상태 관리 라이브러리를 설치하는 것을 익숙해하지만 정작 왜 상태 관리가 필요한지, 또 이 상태 관리가 어떻게 리액트와 함께 작동하는지는 간과하는 경우가 많다. 이번 장에서는 상태 관리 라이브러리의 필요성부터 최근 많이 주목받고 있는 상태 관리 라이브러리가 어떻게 작동하는지 살펴본다.

### 5.1 상태 관리는 왜 필요 한가?

-  상태 관리에 대해 이야기하기에 앞서 이제 앞으로 계속해서 이야기할 ‘상태’가 무엇인지 정의할 필요가 있다. 흔히 웹 애플리케이션을 개발할 때 이야기하는 상태는 어떠한 의미를 지닌 값이며 애플리케이션의 시나리오
에 따라 지속적으로 변경될 수 있는 값을 의미한다. 웹 애플리케이션에서 상태로 분류될 수 있는 것들은 대표적으로 다음과 같은 것이 있다.
    - **UI： 기본적으로 웹 애플리케이션에서 상태라 함은 상호 작용이 가능한 모든 요소의 현재 값을 의미**한다. 다크/라이트 모드, 라디오를 비롯한 각종 input, 알림창의 노출 여부 등 많은 종류의 상태가 존재한다.
    - URL： 브라우저에서 관리되고 있는 상태값으로, 여기에도 우리가 참고할 만한 상태가 존재할 수 있다. https://www.airbnb.co.kr7rooins/34：L13796?adults=2와 같은 주소가 있다고 가정해 보자. 이 주소에는 rooinld=34113796과 adults=2라고 하는 상태가 존재하며 이 상태는 사용자의 라우팅에 따라 변경된다.
    - 폼(form): 폼에도 상태가 존재한다. 로딩 중인지(loading), 현재 저g됐는지(submit). 접근이 불7|능한지(disabled), 값이 유효한지(validation) 등 모두가 상태로 관리된다.
    - 서버에서 가져온 값 클라이언트에서 서버로 요청을 통해 가져온 값도 상태로 볼 수 있다. 대표적으로 API 요청이 있다.

- 애플리케이션 전체적으로 관리해야 할 상태가 있다고 가정해 보자. 그리고 그 상태에 따라 다양한 요소들이 각 상태에 맞는 UI를 보여줘야 한다. 상태를 어디에 둘 것인가? 전역 변수에 둘 것인가? 별도의 클로저를 만들 것인가? 그렇다면 그 상태가 유효한 범위는 어떻게 제한할 수 있을까? 상태의 변화에 따라 변경돼야 하는 자식 요소들은 어떻게 이 상태의 변화를 감지할 것인가? 이러한 상태 변화가 일어남에 따라 즉각적으로 모든 요소들이 변경되어 애플리케이션이 찢어지는 현상(이를 tearing이라고 하며, 하나의 상태에 따라 서로 다른 결과물을 사용자에게 보여주는 현상을 말한다)을 어떻게 방지할 것인가?
- 이처럼 현대 웹 애플리케이션에서 상태 관리란 어렵다고 흐fl서 외면할 수 없는 주제가 됐다. 이러한 상태를 효율적으로 관리하고, 상태가 필요한 쪽에서는 빠르게 반응할 수 있는 모델에 대한 고민이 본격적으로 시작된 것이다.

#### 5.1.1 리액트 상태관리의역사

- 다른 웹 개발 환경과 마찬가지로 리액트도 상태 관리에 대한 필요성이 존재했다. 애플리케이션 개발에 모든 것을 제공하는, 이른바 프레임워크를 지향하는 Angular와는 다르게 **리액트는 단순히 사용자 인터페이스를 만들기 위한 라이브러리일 뿐이고, 그 이상의 기능을 제공하지 않고 있다..** 따라서 상태를 관리하는 방법도 개발자에 따라, 시간에 따라 많은 차이가 있다. 리액트 생태계에서 개발자들이 상태 관리를 하기 위해 어떠한 방법을 활용했는지 그 역사를 살펴보자.

<h5>Flux 패턴의 등장</h5>

- 리액트에서는 상태 관리, 특히 전역 상태 관리를 어떻게 했을까? 리덕스가 나타나기 전까지 리액트 애플리케이션에서 딱히 이름을 널리 알린 상태 관리 라이브러리는 없었다.
- 그러던 중 2014년경, 리액트의 등장과 비슷한 시기에 Flux 패턴과 함께 이를 기반으로 한 라이브러인 Flux를 소개하게 된다. Flux에 대해 소개하기에 앞서 먼저 이 당시 웹 개발상황을 짚고 넘어가자. **웹 애플리케이션이 비대해지고 상태（데이터）도 많아짐에 따라 어디서 어떤 일이 일어나서 이 상태가 변했는지 등을 추적하고 이해하기가 매우 어려운 상황이었다.**
- 페이스북 팀은 이러한 문제의 원인을 양방향 데이터 바인딩으로 봤다. **뷰（HTML）가 모델（자바스크립트）을 변경할 수 있으며, 반대의 경우 모델도 뷰를 변경할 수 있다. 이는 코드를 작성하는 입장에서는 간단할 수 있지만 코드의 양이 많아지고 변경 시나리오가 복잡해질수록 관리가 어려워진다.** 페이스북 팀은 양방향이 아닌 단방향으로 데이터 흐름을 변경하는 것을 제안하는데 이것이 바로 Flux 패턴의 시작이다.

<div><img src= "/assets/img/post/flux_flow.PNG"></div>

-  각 용어의 정의를 살펴보자.
    - 액션(action): 어떠한 작업을 처리할 액션과 그 액션 발생 시 함께 포함시킬 데이터룰 의미한다. 액션 타입과 데이터를 각각 정의해 이를 디스패처로 보낸다.
    - 디스패처(dispatcher): 액션을 스토어에 보내는 역할을 한다. 콜백 함수 형태로 앞서 액션이 정의한 타입과 데이터를 모두 스토어에 보낸다.
    - 스토어(store): 여기에서 실제 상태에 따른 값과 상태를 변경할 수 있는 메서드를 가지고 있다. 액션의 타입에 따라 어떻게 이를 변경할지가 정의돼 있다.
    - 뷰(view)： 리액트의 컴포넌트에 해당하는 부분으로. 스토어에서 만들어진 데이터를 가져와 화면을 렌더링하는 역할을 한다. 또한 뷰에서도 사용자의 입력이나 행위에 따라 상태를 업데이트하고자 할 수 있을 것이다. 이 경우에는 다음 그림처럼 뷰에서 액션을 호출하는 구조로 구성된다.

<div><img src= "/assets/img/post/flux_flow2.PNG"></div>

- 간단하게 리액트 코드로 살펴보자.

```js
type storestate = {
count: number
}

type Action = { type: 'add'; payload: number }

function reducer(prevState: StoreState, action; Action) {
    const { type: ActionType } = action
    if (ActionType === 'add') {
        return {
            count: prevState.count + action.payload,
        }
    }
    throw new Error('Unexpected Action [${ActionType}]')
}

export default function App() {
    const [state, dispatcher] = useReducer(rediicer, { count： 0 })
    function handleClick() {
        dispatcher({ type: 'add', payload： 1 })
    }
    return (
        <div>
            <hl>{state.count}</hl>
            <button onClick={handleClick}>+</button>
        </div>
    )
}
```

- 먼저 type Action으로 액션이 어떤 종류가 있고 어떤 데이터를 필요로 하는지 정의해 뒀다. 그리고 스토어의 역할을 하는 것이 useReducer와 reducer인데, 각각 현재 상태와 상태에 따른 값이 어떻게 변경되는지를 정의했다. 그리고 dispatcher로 이 액션을 실행했고, 이를 뷰인 App에서 보여준다.
- 이러한 단방향 데이터 흐름 방식은 당연히 불편함도 존재한다. 사용자의 입력에 따라＜여기에서는 사용자의 클릭에 따라） 데이터를 갱신하고 화면을 어떻게 업데이트해야 하는지도 코드로 작성해야 하므로 코드의 양이 많아지고 개발자도 수고로워진다. 그러나 데이터의 흐름은 모두 액션이라는 한 방향（단방향）으로 줄어들므로 데이터의 흐름을 추적하기 쉽고 코드를 이해하기가 한결 수월해진다.
- 상태와 그 상태의 변경에 대한 흐름과 방식을 단방향으로 채택한 것이 바로 리액트 기반 Flux의 특징이라고 볼 수 있다.

<h5>Context API와 useContext</h5>

- 리액트가 처음 세상에 나온 뒤에도 상태를 어떻게 적절하게 주입해야 하는지에 대한 고민은 계속 이어져왔다. 부모에 있는 상태를 자식 컴포넌트에서 쓰기 위해서는 이른바 prop 내려주기 라 불리는 방식. 즉 props를 가지고 있는 부모에서 필요한 자식 컴포넌트까지 끊임없이 컴포넌트의 인수로 넘겨야 하는 불편함이 있었다.
- 리액트 팀은 리액트 16.3에서 전역 상태를 하위 컴포넌트에 주입할 수 있는 새로운 Context API를 출시했다. props로 상태를 넘겨주지 않더라도 Context API를 사용하면 원하는 곳에서 Context Provider가 주입하는 상태를 사용할 수 있게 된 것이다.
- 그러나 3.1 절 ‘리액트의 모든 훅 파헤치기’에서 이야기한 것처럼 Context API는 상태 관리가 아닌 주입을 도와주는 기능이며, 렌더링을 막아주는 기능 또한 존재하지 않으니 사용할 때 주의가 필요하다.

### 5.2 리액트 훅으로 시작하는 상태 관리

#### 5.2.1 가장 기본적인 방법: useState와 useReducer

- usestate의 등장으로 리액트에서는 여러 컴포넌트에 걸쳐 손쉽게 동일한 인터페이스의 상태를 생성하고 관리할 수 있게 됐다. 다음 예제 훅을 살펴보자.

```js
function useCoi』nter(initCount: number = 0) {
    const [counter, setCounter] = useState(initCount)
    function inc() {
        setCounter((prev) => prev + 1)
    }

    return { counter, inc }
}
```

- 이 예제는 usecounter라는 훅을 만들어서 함수형 컴포넌트 어디에서든 사용할 수 있게 구현한 사례다. 이 훅은 외부에서 받은 숫자 혹은 0을 초깃값으로 상태를 관리하며, inc라는 함수를 선언해 이 숫자를 1씩 증가시킬 수 있게 구현했다. 그리고 상태값인 counter와 inc 함수를 객체로 반환한다.
- 다음 코드와 같이 usecounter를 사용하는 함수형 컴포넌트는 이 훅을 사용해 각자의 counter 변수를 관리하며, 중복되는 로직 없이 숫자를 1 씩 증가시키는 기능을 손쉽게 이용할 수 있다.

```js
function useCounter(initCount: number = 0) {
    const [counter, setCounter] = useState(initCount)
    function inc() {
        setCounter((prev) => prev + 1)
    }
    return { counter, inc }
}
function Counter1() {
    const { counter, inc } = useCounter()
    return (
        <>
            <h3>Counter1: {counter}</h3>
            <button onClick={inc}>+</button>
        </>
    )
}
function Counter2() {
    const { counter, inc } = useCounter()
    return (
        <>
            <h3>Counter2: {counter}</h3>
            <button onClick={inc}>+</button>
        </>
    )
}
```

- usecounter라는 훅이 없었다면 이러한 기능이 필요한 각각의 컴포넌트에서 모두 위와 같은 내용을 구현해야만 했을 것이다. 더 나아가 훅 내부에서 관리해야 하는 상태가 복잡하거나 상태를 변경할 수 있는 시나리오가 다양해진다면 훅으로 코드를 격리해 제공할 수 있다는 장점이 더욱 크게 드러날 것이다. 이처럼 리액트의 훅을 기반으로 만든 사용자 정의 훅은 함수형 컴포넌트라면 어디서든 손쉽게 재사용 가능하다는 장점이 있다.
- 지금까지 일반적으로 사용되는 usestate와 useReducer로 컴포넌트 내부의 상태를 관리하는 방법에 대해 알아봤다. 그러나 실제 애플리케이션을 작성해 보면 알겠지만 usestate와 useReducer가 상태 관리의 모든 필요성과 문제를 해결해 주지는 않는다. usestate와 useReducer를 기반으로 하는 사용자 지정 훅의 한계는 명확하다. **훅을 사용할 때마다 컴포넌트별로 초기화되므로 컴포넌트에 따라 서로 다른 상태를 가질 수밖에 없다.** 위 예제의 경우 counter는 useCounter가 선언될 때마다 새롭게 초기화되어, 결론적으로 컴포넌트별로 상태의 파편화를 만들어 버린다. 이렇게 기본적인 usestate를 기반으로 한 상태를 지역 상태(local state)라고 하며, 이 지역 상태는 해당 컴포넌트 내에서만 유효하다는 한계가 있다.
- 만약 usecounter에서 제공하는 counter를 올리는 함수는 지금처럼 동일하게 사용하되, 두 컴포넌트가 동일한 counter 상태를 바라보게 하기 위해서는 어떻게 해야 할까? **즉, 현재 지역 상태인 counter를 여러 컴포넌트가 동시에 사용할 수 있는 전역 상태(global state)로 만들어 컴포넌트가 사용하는 모든 훅이 동일한 값을 참조할 수 있게 하려면 어떻게 해야 할까?** 가장 먼저 떠오르는 방법은 상태를 컴포넌트 밖으로 한 단계 끌어올리는 것이다. 다음 예제를 보자.

```js
function Counter1({ counter, inc }: { counter: number; inc: () => void }) {
    return (
        <>
            <h3>Counterl: {counter}</h3>
            <button onClick={inc}>+</button>
        </>
    )
}
function Counter2({ counter, inc }: { counter: number; inc: () => void }) {
    return (
        <>
            <h3>Counter2: {counter}</h3>
            <button onClick={inc}>+</button>
        </>
    )
}

function Parent() {
    const { counter, inc } = useCounter()
    return (
        <>
            <Counter1 counter={counter} inc={inc} />
            <Counter2 counter={counter} inc={inc} />
        </>
    )
}
```

- 예제에서는 useCounter를 각 컴포넌트에서 사용하는 대신, Parent라고 불리는 상위 컴포넌트에서만 useCounter를 사용하고, 이 훅의 반환값을 하위 컴포넌트의 props로 제공했다. 즉, 지역 상태인 useCounter를 부모 컴포넌트로 한 단계 끌어올린 다음, 이 값을 하위 컴포넌트에서 참조해 재사용하게끔 만들었다. 이제 적어도 Parent 내부에서는 위의 props 규칙만 잘 지킨다면 하나의 counter 값과 하나의 inc 함수로 상태를 관리할 수 있게 된다.
- **여러 컴포넌트가 동일한 상태를 사용할 수 있게 됐다는 점은 주목할 만하지만 props 형태로 필요한 컴포넌트에 제공해야 한다는 점은 여전히 조금은 불편해 보인다.** 이후에 이러한 점을 어떻게 개선할 수 있을지 살펴보자.
- 지금까지 usestate와 useReducer, 그리고 사용자 지정 훅을 활용한 지역 상태 관리를 살펴봤다. **이 두 훅은 만들기에 따라 재사용할 수 있는 지역 상태를 만들어 주지만 이는 지역 상태라는 한계 때문에 여러 컴포넌트에 걸쳐 공유하기 위해서는 컴포넌트 트리를 재설계하는 등의 수고로움이 필요하다.**

#### 5.2.2 지역 상태의 한계를 벗어나보자: useState의 상태를 바깥으로 분리하기

- 리액트 코드의 작동 여부를 떠나서 조금 더 과감하게 상상해 보자. usestate의 한계는 명확하다. 현재 리액트의 usestate는 리액트가 만든 클로저 내부에서 관리되어 지역 상태로 생성되기 때문에 해당 컴포넌트에서만 사용할 수 있다는 단점이 있다. 만약 usestate가 이 리액트 클로저가 아닌 다른 자바스크립트 실행 문맥 어디에선가, 즉 완전히 다른 곳에서 초기화돼서 관리되면 어떨까? 즉, 어딘가에서 해당 값을 업데이트하면 그 값을 참조하고 있는 컴포넌트나 훅에서도 그 업데이트된 값을 사용할 수도 있지 않을까? 이를테면 다음과 같이 관리하는 상상을 해보자. 먼저 counter.ts라는 별개의 파일을 생성해서 다음과 같이 코드를 작성해 보자.

```js
// counter.ts
export type State = { counter: number }
// 상태를 아예 컴포넌트 밖에 선언했다. 각 컴포넌트가 이 상태를 바라보게 할 것이다.
let state: State = {
    counter: 0,
}
// getter
export function get(): State {
    return state
}
// usestate와 동일하게 구현하기 위해 게으른 초기화 함수나 값을 받을 수 있게 했다.
type Initializer<T> = T extends any ? T | ((prev: T) => T) : never

// setter
export function set<T>(nextState: Initializer<T>) {
    state = typeof nextState === function' ? nextState(state) : nextState
}

// Counter
function Counter() {
    const state = get()

    function handleClick() {
        set((prev: State) => ({ counter: prev.counter + 1 }))
    }
    return (
        <>
            <h3>{state.counter}</h3>
            <button onClick={handleClick}>+</button>
        </>
    )
}
```

- 언뜻 보면 이러한 방식은 작동할 것 같지만 아쉽게도 위 방식은 리액트 환경에서 작동하지 않는다. setter나 getter 등의 코드가 잘못돼서가 아니다. **그러나 가장 큰 문제는 컴포넌트가 리렌더링되지 않는다는 것이다. 원인은 바로 리액트의 렌더링 방식 때문이다.** 새로운 상태를 사용자의 UI에 보여주기 위해서는 반드시 리렌더링이 필요하다. 이 리렌더링은 함수형 컴포넌트의 재실행(호출), usestate의 두 번째 인수 호출 등 다양한 방식으로 일어나지만 위 코드에서는 리렌더링을 일으키는 장치가 어디에도 존재하지 않는다.
- 즉, 업데이트되는 값을 가져오려면 상태를 업데이트하는 것뿐만 아니라 상태가 업데이트됐을 때 이를 컴포넌트에 반영시키기 위한 리렌더링이 필요하며, 함수형 컴포넌트에서 리렌더링을 하려면 다음과 같은 작업 중 하나가 일어나야 한다.
    - usestate, useReducer의 반환값 중 두 번째 인수가 어떻게든 호출된다. 설령 그것이 컴포넌트 렌더링과 관계없는 직접적인 상태를 관리하지 않아도 상관없다. 어떠한 방식으로든 두 번째 인수가 호출되면 리액트는 다시 컴포넌트를 렌더링 한다.
    - 부모 함수(부모 컴포넌트)가 리렌더링되거나 해당 함수(함수형 컴포넌트)가 다시 실행돼야 한다. 그러나 위 경우 부모 컴포넌트가 없으며, props도 없기 때문에 일일이 Counter()를 재실행시켜야 하지만 그것은 매우 비효율적이다.
- 여기서 우리가 시도해 볼 수 있는 것은 usestate와 useReducer뿐으로 보인다. 그렇다면 useState의 인수로 컴포넌트 밖에서 선언한 state를 넘겨주는 방식으로 코드를 변경해 보자.

```js
function Counter1() {
    const [count, setCount] = useState(state)
    function handleClick() {
        // 외부에서 선언한 set 함수 내부에서 다음 상태값을 연산한 다음,
        // 그 값을 로컬 상태값에도 넣었다.
        set((prev： State) => {
            const newState = { counter: prev.counter + 1 }
            // setcount가 호출되면서 컴포넌트 리렌더링을 야기한다.
            setCount(newState)
            return newState
        })
    }
    return (
        <>
            <h3>{count.counter}</h3>
            <button onClick={handleClick}>+</button>
        </>
    )
}

function Counter2() {
    const [count, setCount] = useState(state)
    // 위 컴포넌트와 동일한 작동올 추가했다.
    function handleClick() {
        set((prev: State) => {
            const newState = { counter: prev.counter + 1 }
            setcount(newState)
            return newState
        })
    }
    return (
        <>
            <h3>{count. coiinter}</h3>
            <button onClick={handleClick}>+</button>
        </>
    )
}
```

- 예제 코드에서는 억지로 전역에 있는 상태를 참조하게 만들었다. usestate의 초깃값으로 컴포넌트 외부에 있는 값을 사용하는 위와 같은 방식은 일반적인 리액트 코드 작성 방식과 동일하다.
- 여기서 독특한 것은 바로 handleclick으로 state를 업데이트하는 방식이다. 기본적으로 useState의 두 번째 인수로 업데이트하는 것은 해당 지역 상태에만 영향을 미치기 때문에 여기서는 외부에 선언한 set을 실행해 외부의 상태값 또한 업데이트하도록 수정했다. 이렇게 외부의 상태를수정하고 usestate의 두 번째 인수도 실행한다면 리액트 컴포넌트는 렌더링될 것이고 우리는 계속해서 외부의 값을 안정적으로 참조할 수 있게 된다.
- 그러나 이 방법은 굉장히 비효율적이고 문제점도 가지고 있다. 외부에 상태가 있음에도 불구하고, **함수형 컴포넌트의 렌더링을 위해 함수의 내부에 동일한 상태를 관리하는 usestate가 존재하는 구조다. 이는 상태를 중복해서 관리하므로 비효율적인 방식**이라고 볼 수 있다.
- 여기에는 또 한 가지 문제점이 있는데, 실제로 각 컴포넌트의 버튼을 누르면 이상하게 작동하는 것을 확인할 수 있다. **버튼을 누르면 해당 컴포넌트가 렌더링되면서 원하는 값을 안정적으로 렌더링하지만 같은 상태를 바라봐야 하는 반대쪽 컴포넌트에서는 렌더링되지 않는다.** 반대쪽 컴포넌트는 버튼을 눌러야 그제서야 렌더링되어 최신값을 불러온다. 그러나 여전히 반대쪽은 렌더링되지 않는 것을 볼 수 있다. 왜 같은 상태를 공유하지만동시에 렌더링되지 않는 것일까?
- usestate로 컴포넌트의 리렌더링을 실행해 최신값을 가져오는 방법은 어디까지나 해당 컴포넌트 자체에서만 유효한 전략이다. **즉, 반대쪽의 다른 컴포넌트에서는 여전히 상태의 변화에 따른 리렌더링을 일으킬 무언가가 없기 때문에 클릭 이벤트가 발생하지 않는 다른 쪽은 여전히 렌더링이 되지 않는다.**
- 이러한 한계를 종합해 본 내용을 살펴보면 함수 외부에서 상태를 참조하고 이를 통해 렌더링까지 자연스럽게 일어나려면 다음과 같은 조건을 만족해야 한다는 결론에 도달한다.
    - 1)  꼭 window나 global에 있어야 할 필요는 없지만 컴포넌트 외부 어딘가에 상태를 두고 여러 컴포넌트가 같이 쓸 수 있어야한다.
    - 2) 이 외부에 있는 상태를 사용하는 컴포넌트는 상태의 변화를 알아챌 수 있어야 하고 상태가 변화될 때마다 리렌더링이 일어나서 컴포넌트를 최신 상태값 기준으로 렌더링해야 한다. 이 상태 감지는 상태를 변경시키는 컴포넌트뿐만 아니라 이 상태를 참조하는 모든 컴포넌트에서 동일하게 작동해야 한다.
    3)  상태가 원시값이 아닌 객체인 경우에 그 객체에 내가 감지하지 않는 값이 변한다 하더라도 리렌더링이 발생해서는 안된다.
- 위와 같은 조건을 만족할 수 있는, 컴포넌트 레벨의 지역 상태를 벗어나는 새로운 상태 관리 코드를 만들어보자. 먼저 이 상태는 객체일 수도, 원시값일 수도 있으므로 좀 더 범용적인 이름인 store로 정의한다. 그리고 2번의 조건을 만족하기 위해서는 store의 값이 변경될 때마다 변경됐음을 알리는 callback 함수를 실행해야 하고, 이 callback을 등록할 수 있는 subscribe 함수가 필요하다.
- 먼저 위 조건을 만족하는 store의 뼈대를 만들어보자. 타입스크립트의 타입을 선언해 두면 직접 코드를 작성하기에 앞서 만들어야 할 함수의 기본적인 타입을 정의해 두고 이야기할 수 있으므로 유용하다.

```js
type Initializer<T> = T extends any ? T | ((prev: T) => T) : never
type Store<State> = {
    get: 0 => State
    set： (action: Initializer<State>) => State
    subscribe: (callback： () => void) => () => void
}
```

- get은 항상 최신값을 가져와야 하므로 함수로 구현했다. 그리고 set의 형태는 기존에 리액트 개발자가 널리 사용하고 있는 usestate와 동일하게 값 또는 함수를 받을 수 있도록 만들었다. 마지막으로 subscribe는 이 store의 변경을 감지하고 싶은 컴포넌트들이 자신의 callback 함수를 등록해 두는 곳이다. callback을 인수로 받으며, store는 값이 변경될 때마다 자신에게 등록된 모든 callback을 실행하게 할 것이다. 그리고 이 스토어를 참조하는 컴포넌트는 subscribe에 컴포넌트 자기 자신을 렌더링하는 코드를 추가해서 컴포넌트가 리렌더링을 실행할수 있게 만들 것이다.
- 뼈대를 만들었으니 이 Store<State> 함수를 실제로 작성해 보자.

```js
export const createStore = <State extends unknown>(
initialstate: Initializer<State>,
)： Store<State> => {
    // usestate와 마찬가지로 초깃값을 게으른 초기화를 위한 함수 또한
    // 그냥 값올 받을 수 았도록 한다.
    // state의 값은 스토어 내부에서 보관해야 하므로 변수로 선언한다.
    let state = typeof initialstate !== 'function' ? initialstate : initialState()
    // callbacks는 자료형에 관계없이 유일한 값을 저장할 수 있는 Set을 사용한다.
    const callbacks = new Set<() => void>()
    // 언제든 get이 호출되면 최신값을 가져올 수 있도록 함수로 만든다.
    const get = () => state
    const set = (nextState: State | ((prev: State) => State)) => {
        // 인수가 함수라면 함수를 실행해 새로운 값을 받고,
        // 아니라면 새로운 값을 그대로 사용한다.
        state =
            typeof nextstate === 'function'
            ? (nextstate as (prev: State) => State)(state)
            : nextstate
        // 값의 설정이 발생하면 콜백 목록올 순회하면서 모든 콜백을 실행한다.
        callbacks.forEach((callback) => callback())

        return state
    }
    // subscribe는 콜백을 인수로 받는다.
    const subscribe = (callback: () => void) => {
        // 받은 함수를 콜백 목록에 추가한다.
        callbacks.add(callback)
        // 클린업 실행 시 이를 삭제해서 반복적으로 추가되는 것을 막는다.
        return () => {
            callbacks.delete(callback)
        }
    }
    return { get, set, subscribe }
}```



<h3>끝!</h3>
