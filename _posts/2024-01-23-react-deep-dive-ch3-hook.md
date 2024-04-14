---
title: 3장 리액트 훅 깊게 살펴보기
categories:
- React Deep Dive
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

함수형 컴포넌트가 상태를 사용하거나 클래스형 컴포넌트의 생명주기 메서드를 대체하는 등의 다양한 작업을 하기 위해 훅(hook)이라는 것이 추가됐다. 훅을 활용하면 클래스형 컴포넌트가 아니더라도 리액트의 다
양한 기능을 활용할 수 있다. 리액트에서 현재 사용 가능한 훅이 무엇이고, 어떻게 쓰이는지, 그리고 훅을 사용할 때 주의할 점은 무엇인지 확인해 보자.

### 3.1 리액트의 모든 훅 파헤치기

- 리액트 함수형 컴포넌트에서 가장 중요한 개념은 바로 훅이다. **훅은 클래스형 컴포넌트에서만 가능했던 state, ref 등 리액트의 핵심적인 기능을 함수에서도 가능**하게 만들었고, 무엇보다 클래스형 컴포넌트보다 간결하게 작성할 수 있어 훅이 등장한 이래로 대부분의 리액트 컴포넌트는 함수형으로 작성되고 있을 정도로 많은 사랑을 받고 있다.

#### 3.1.1 useState

- useState는 함수형 컴포넌트 내부에서 상태를 정의하고, 이 상태를 관리할 수 있게 해주는 훅이다.
- 간단한 예제를 살펴보자. 

```js
function Component() {
    const [, triggerRender] = useState()
    let state = 'hello'
    function handleButtonClickO {
        state = 'hi'
        triggerRender()
    }
    return (
        <>
        <hl>{state}</hl>
        <button onClick={handleButtonClick}>hi</button>
        </>
    )
}
```

- usestate 반환값의 두 번째 원소를 실행해 리액트에서 렌더링이 일어나게끔 변경했다. 그럼에도 여전히 버튼 클릭 시 state의 변경된 값이 렌더링되고 있지 않다. state가 업데이트되고 있는데 왜 렌더링이 되지 않을까?
- **그 이유는 리액트의 렌더링은 함수형 컴포넌트에서 반환한 결과물인 return의 값을 비교해 실행되기 때문이다.** 즉, 매번 렌더링이 발생될 때마다 함수는 다시 새롭게 실행되고, 새롭게 실행되는 함수에서 state는 매번 hello로 초기화되므로 아무리 state를 변경해도 다시 hello로 초기화되는 것이다.
- 지금까지는 이해할 수 있는 과정으로 보인다. **함수형 컴포넌트는 매번 함수를 실행해 렌더링이 일어나고, 함수 내부의 값은 함수가 실행될 때마다 다시 초기화된다. 그렇다면 usestate 훅의 결괏값은 어떻게 함수가 실행돼도 그 값을 유지하고 있을까?**

- 리액트의 내부 구현을 하나도 모른다고 가정하고 usestate가 어떤 구조를 가지고 있을지 상상해 보자.

```js
function useState(initialValue) {
    let internalstate = initialVaTue
    function setState(newValue) {
        internalstate = newValue
    }
    return [internalstate, setState]
}
```

- 그러나 이는 우리가 원하는 대로 작동하지 않는다.

```js
const [value, setValue] = useState(0)
setValue(l)
console.log(value) // 0
```

- 이러한 결과가 발생하는 이유는 setvalue로 값을 변경했음에도 이미 구조 분해 할당으로 state의 값, **즉 value를 이미 할당해 놓은 상태이기 때문에 훅 내부의 setstate를 호출하더라도 변경된 새로운 값을 반환하지는 못한 것이다.**
- 이를 해결하려면 먼저 state를 함수로 바꿔서 state의 값을 호출할 때마다 현재 state를 반환하게 하면 된다.

```js
function useState(initialValue) {
    let internalstate = initialvalue
    function state() {
        return internalstate
    }
    function setState(newValue) {
        internalstate = newValue
    }
    return [state, setState]
}

const [value, setValue] = useState(0)
setValue(l)
console.log(value()) // 1
```

- 물론 이것은 우리가 사용하는 usestate 훅의 모습과는 많이 동떨어져 있다. 우리는 state를 함수가 아닌 상수처럼 사용하고 있기 때문이다.
- 이를 해결하기 위해 **리액트는 클로저를 이용했다. 여기서 클로저는 어떤 함수(usestate) 내부에 선언된 함수(setstate)가 함수의 실행이 종료된 이후에도(useState가 호출된 이후에도) 지역변수인 state를 계속 참조할 수 있다는 것을 의미**한다.

<h5>게으른 초기화</h5>

- 일반적으로 usestate에서 기본값을 선언하기 위해 useState() 인수로 원시값을 넣는 경우가 대부분일 것이다. . useState에 변수 대신 함수를 넘기는 것을 게으른 초기화＜lazy initialization）라고 한다. 이 게으른 초기화가 무엇인지 살펴보자.

```js
// 일반적인 useState 사용
// 바로 값을 집어넣는다.
const [count, setCount] = useState(
    Number.parselnt(window.localstorage.getltem(cacheKey)),
)
// 게으른 초기화
// 위 코드와의 차이점은 함수를 실행해 값을 반환한다는 것이다.
const [count, setCount] = useState(() =>
    Number.parselnt(window.localstorage.getltem(cacheKey)),
)
```

- **리액트 공식 문서2에서 이러한 게으른 초기화는 usestate의 초깃값이 복잡하거나 무거운 연산을 포함하고 있을 때 사용하라고 돼 있다. 이 게으른 초기 함수는 오로지 state가 처음 만들어질 때만 사용된다.** 만약 이후에 리렌더링이 발생된다면 이 함수의 실행은 무시된다. 다음 예제를 보자.
- 리액트에서는 렌더링이 실행될 때마다 함수형 컴포넌트의 함수가 다시 실행된다는 점을 명심하자. 함수형 컴포넌트의 usestate의 값도 재실행된다. 물론 우리는 앞서 구현 예제를 통해 내부에는 클로저가 존재하며, 클로저를 통해 값을 가져오며 초깃값은 최초에만 사용된다는 것을 알고 있다. 만약 usestate 인수로 자바스크립트에 많은 비용을 요구하는 작업이 들어가 있다면 이는 계속해서 실행될 위험이 존재할 것이다. 그러나 **우려와는 다르게 usestate 내부에 함수를 넣으면 이는 최초 렌더링 이후에는 실행되지 않고 최초의 state 값을 넣을 때만 실행된다.**
- 만약 Number.parselnt(window.localstorage.getltem(cacheKey))와 같이 한 번 실행되는 데 어느 정도 비용이 드는 값이 있다고 가정해 보자. usestate의 인수로 이 값 자체를 사용한다면 초깃값이 필요한 최초 렌더링과, 초깃값이 있어 더 이상 필요 없는 리렌더링 시에도 동일하게 계속 해당 값에 접근해서 낭비가 발생한다. 따라서 이런 경우에는 함수 형태로 인수에 넘겨주는 편이 훨씬 경제적일 것이다.
- 그렇다면 게으른 최적화는 언제 쓰는 것이 좋을까? 리액트에서는 무거운 연산이 요구될 때 사용하라고 한다. **즉, localStorage나 sessionstorage에 대한 접근. map, filter, find 같은 배열에 대한 접근, 혹은 초깃값 계산을 위해 함수 호출이 필요할 때와 같이 무거운 연산을 포함해 실행 비용이 많이 드는 경우에 게으른 초기화를 사용하는 것이 좋다.**

#### 3.1.2 useEffect

- useEffect는 자주 쓰지만 생각보다 사용하기 쉬운 훅이 아니다. 그리고 알려진 것처럼 생명주기 메서드를 대체하기 위해 만들어진 훅도 아니다.
- **useEffect의 정의를 정확하게 내리자면 useEffect는 애플리케이션 내 컴포넌트의 여러 값들을 활용해 동기적으로 부수 효과를 만드는 메커니즘이다.** 그리고 이 부수 효과가 ‘언제’ 일어나는지보다 어떤 상태값과 함께 실행되는지 살펴보는 것이 중요하다.
- 의존성 배열이 변경될 때마다 useEffect의 첫 번째 인수인 콜백을 실행한다는 것은 널리 알려진 사실이다. **하지만 useEffect는 어떻게 의존성 배열이 변경된 것을 알고 실행될까?** 여기서 한 가지 기억해야 사실은 바로 함수형 컴포넌트는 매번 함수를 실행해 렌더링을 수행한다는 것이다. 다음 예제 코드를 살펴보자.
- 함수형 컴포넌트는 렌더링 시마다 고유의 state와 props 값을 갖고 있다. useEffect는 자바스크립트의 proxy나 데이터 바인딩, 옵저버 같은 특별한 기능을 통해 값의 변화를 관찰하는 것이 아니고 **렌더링할 때마다 의존성에 있는 값을 보면서 이 의존성의 값이 이전과 다른 게 하나라도 있으면 부수 효과를 실행하는 평범한 함수**라 볼 수 있다.  따라서 **useEffect는 state와 props의 변화 속에서 일어나는 렌더링 과정에서 실행되는 부수 효과 함수라고 볼 수 있다.**

<h5>클린업 함수의 목적</h5>

- 그렇다면 이른바 클린업 함수라 불리는 useEffect 내에서 반환되는 함수는 정확히 무엇이고 어떤 일을 할까? 일반적으로 이 클린업 함수는 이벤트를 등록하고 지울 때 사용해야 한다고 알려져 있다.

```js
useEffect(() => {
function addMouseEvent() {
    console.log(counter)
}
window.addEventListener('click', addMouseEvent)
// 클린업 함수
return () => {
    console.log('클린업 함수 실행!', counter)
    window. removeEventListerier('click', addMouseEvent)
}
}, [counter])
```

- 위 useEffeet가 포함된 컴포넌트를 실행해 보면 다음과 같은 결과를 얻을 수 있다.

```js
클린업 함수 실행! 0
1

클린업 함수 실행! 1
2

클린업 함수 실행! 2
3

클린업 함수 실행! 3
4

// ...
```

- 위 로그를 살펴보면 클린업 함수는 이전 counter 값, 즉 이전 state를 참조해 실행된다는 것을 알 수 있다. 클린업 함수는 새로운 값과 함께 렌더링된 뒤에 실행되기 때문에 위와 같은 메시지가 나타난다. 
- **여기서 중요한 것은 클린업 함수는 비록 새로운 값을 기반으로 렌더링 뒤에 실행되지만 이 변경된 값을 읽는 것이 아니라 함수가 정의됐을 당시에 선언됐던 이전 값을 보고 실행된다는 것이다.**
- 이 사실을 종합해 보면 왜 useEffeet에 이벤트를 추가했을 때 클린업 함수에서 지워야 하는지 알 수 있다. **함수형 컴포넌트의 useEffect는 그 콜백이 실행될 때마다 이전의 클린업 함수가 존재한다면 그 클린업 함수를 실행한 뒤에 콜백을 실행한다.** 따라서 이벤트를 추가하기 전에 이전에 등록했던 이벤트 핸들러를 삭제하는 코드를 클린업 함수에 추가하는 것이다. 이렇게 함으로써 특정 이벤트의 핸들러가 무한히 추가되는 것을 방지할 수 있다.
- **클린업 함수는 언마운트라기보다는 함수형 컴포넌트가 리렌더링됐을 때 의존성 변화가 있었을 당시 이전의 값을 기준으로 실행되는, 말 그대로 이전 상태를 청소해 주는 개념으로 보는 것이 옳다.**

<h5>의존성 배열</h5>

- 의존성 배열이 없는 useEffect가 매 렌더링마다 실행된다면 그냥 useEffect 없이 써도 되는 게 아닐까?

```js
// 1
function Component() {
    console.log('렌더링됨')
}
// 2
function Component() {
    useEffect(() => {
        console.log('렌더링됨')
    })
}
```

- 두 코드는 명백히 리액트에서 차이점을 지니고 있다. 차이점은 다음과 같다.
    - 1. 이후에 소개할 서버 사이드 렌더링 관점에서 useEffect는 클라이언트 사이드에서 실행되는 것을 보장해 준다. useEffect 내부에서는 window 객체의 접근에 의존하는 코드를 사용해도 된다.
    - 2. **useEffect는 컴포넌트 렌더링의 부수 효과, 즉 컴포넌트의 렌더링이 완료된 이후에 실행된다. 반면 직접 실행은 컴포넌트가 렌더링되는 도중에 실행된다.** 따라서 1번과는 달리 서버 사이드 렌더링의 경우에 서버에서도 실행된다. 그리고 이 작업은 함수형 컴포넌트의 반환을 지연시키는 행위다. 즉, 무거운 작업일 경우 렌더링을 방해하므로 성능에 악영향을 미칠 수있다.

- useEffect의 effect는 컴포넌트의 사이드 이펙트, 즉 부수 효과를 의미한다는 것을 명심하자. **useEffect는 컴포넌트가 렌더링된 후에 어떠한 부수 효과를 일으키고 싶을 때 사용하는 훅이다.**

<h5>useEffect를 사용할 때 주의할 점</h5>

- useEffect는 리액트 코드를 작성할 때 가장 많이 사용하는 훅이면서 가장 주의해야 할 훅이기도 하다.
- eslint-disable-line react-hooks/exhaustive-deps 주석은 최대한 자제하라. 이 ESLint 룰은 useEffect 인수 내부에서 사용하는 값 중 의존성 배열에 포함돼 있지 않은 값이 있을 때 경고를 발생시킨다. 이 코드를 사용하는 대부분의 예제가 빈 배열 []을 의존성으로 할 때, 즉 컴포넌트를 마운트하는 시점에만 무언가를 하고 싶다라는 의도로 작성하곤 한다. 그러나 이는 클래스형 컴포넌트의 생명주기 메서드인 componentDidMount에 기반한 접근법으로, 가급적이면 사용해선 안 된다.
- **useEffect는 반드시 의존성 배열로 전달한 값의 변경에 의해 실행돼야 하는 훅이다. 그러나 의존성 배열을 넘기지 않은 채 콜백 함수 내부에서 특정 값을 사용한다는 것은, 이 부수 효과가 실제로 관찰해서 실행돼야 하는 값과는 별개로 작동한다는 것을 의미한다.** 즉, 컴포넌트의 state, props와 같은 어떤 값의 변경과 useEffect의 부수 효과가 별개로 작동하게 된다는 것이다. useEffect에서 사용한 콜백 함수의 실행과 내부에서 사용한 값의 실제 변경 사이에 연결 고리가 끊어져 있는 것이다.
- 따라서 **정말로 의존성으로 []가 필요하다면 최초에 함수형 컴포넌트가 마운트됐을 시점에만 콜백 함수 실행이 필요한지를 다시 한번 되물어봐야 한다.** 만약 정말 ‘그렇다’라고 하면 useEffect 내 부수 효과가 실행될 위
치가 잘못됐을 가능성이 크다. 다음 예제를 보자.

```js
function Component({ log }: { log: string }) {
    useEffect(() => {
        logging(log)
    }, []) // eslint-disable-line react-hooks/exhaustive-deps
}
```

- 위 코드는 log가 최초로 props로 넘어와서 컴포넌트가 최초로 렌더링된 시점에만 실행된다. **그러나 위 코드는 당장은 문제가 없을지라도 버그의 위험성을 안고 있다. log가 아무리 변하더라도 useEffect의 부수 효과는 실행되지 않고, useEffect의 흐름과 컴포넌트의 props.log의 흐름이 맞지 않게 된다.**
- 따라서 앞에서 logging이라는 작업은 log를 props로 전달하는 부모 컴포넌트에서 실행되는 것이 옳을지도 모른다. **부모 컴포넌트에서 Component가 렌더링되는 시점을 결정하고 이에 맞게 log 값을 넘겨준다면 useEffect의 해당 주석을 제거해도 위 예제 코드와 동일한 결과를 만들 수 있고 Component의 부수 효과 흐름을 거스르지 않을 수 있다.**
- useEffect에 빈 배열을 넘기기 전에는 정말로 useEffect의 부수 효과가 컴포넌트의 상태와 별개로 작동해야만 하는지, 혹은 여기서 호출하는 게 최선인지 한 번 더 검토해 봐아 한다.
- 빈 배열이 아닐 때도 마찬가지다. 만약 특정 값을 사용하지만 해당 값의 변경 시점을 피할 목적이라면 메모이제이션을 적절히 활용해 해당 값의 변화를 막거나 적당한 실행 위치를 다시 한번 고민해 보는 것이 좋다.

<h5>useEffect의 첫 번째 인수에 함수명을 부여하라</h5>

- useEffect를 사용하는 많은 코드에서 useEffect의 첫 번째 인수로 익명 함수를 넘겨준다. 이는 리액트 공식문서도 마찬가지다.

```js
useEffect(() => {
    logging(user.id)
}, [user.id])
```

- useEffect의 코드가 복잡하고 많아질수록 무슨 일을 하는 useEffect 코드인지 파악하기 어려워진다. 이때 이 **useEffect의 인수를 익명 함수가 아닌 적절한 이름을 사용한 기명 함수로 바꾸는 것이 좋다. 우리가 변수에 적절한 이름을 붙이는 이유는 해당 변수가 왜 만들어졌는지 파악하기 위함이다.** useEffect도 마찬가지로 적절한 이름을 붙이면 해당 useEffect의 목적을 파악하기 쉬워진다.

```js
useEffect(
    function logActiveUser() {
        logging(user.id)
    },
    [user.id],
)
```

<h5>거대한 useEffect를 만들지 마라</h5>

- useEffect는 의존성 배열을 바탕으로 렌더링 시 의존성이 변경될 때마다 부수 효과를 실행한다. 이 부수 효과의 크기가 커질수록 애플리케이션 성능에 악영향을 미친다. 비록 useEffect가 컴포넌트의 렌더링 이후에 실행되기 때문에 렌더링 작업에는 영향을 적게 미칠 수 있지만 여전히 자바스크립트 실행 성능에 영향을 미친다는 것은 변함없다.
- 가능한 한 useEffect는 간결하고 가볍게 유지하는 것이 좋다. **만약 부득이하게 큰 useEffect를 만들어야 한다면 적은 의존성 배열을 사용하는 여러 개의 useEffect로 분리하는 것이 좋다.** 만약 의존성 배열이 너무 거대하고 관리하기 어려운 수준까지 이른다면 정확히 이 useEffeet가 언제 발생하는지 알 수 없게 된다.

<h5>불필요한 외부 함수를 만들지 마라</h5>

- useEffect의 크기가 작은 것과 같은 맥락에서 useEffect가 실행하는 콜백 또한 불필요하게 존재해서는 안된다.

```js
useEffect(() => {
    fetchlnfonnation(id)
    return () => controUerRef.current?.abort()
}, [id, fetchinformation])
```

- 이 컴포넌트는 props를 받아서 그 정보를 바탕으로 API 호출을 하는 useEffect를 가지고 있다. **그러나 useEffect 밖에서 함수를 선언하다 보니 불필요한 코드가 많아지고 가독성이 떨어졌다.**

```js
useEffect(() => {
    const controller = new AbortController()
    ;(async () => {
        const result = await fetchlnfo(id, { signal: controller.signal })
        setlnfo(await result.json())
    })()
    return () => controller.abort()
}, [id])
```

- useEffect 외부에 있던 관련 함수를 내부로 가져왔더니 훨씬 간결한 모습이다. useEffect 내에서 사용할 부수 효과라면 내부에서 만들어서 정의해서 사용하는 편이 훨씬 도움이 된다.

#### 3.1.3 useMemo

- **useMemo는 비용이 큰 연산에 대한 결과를 저장(메모이제이션)해 두고, 이 저장된 값을 반환하는 훅이다.** 흔히 리액트에서 최적화를 떠올릴 때 가장 먼저 언급되는 훅이 바로 useMemo다.

```js
import { 나seMemo } from 'react'
const memoizedValue = useMemo(() => expensiveComputation(a, b), [a, b])
```

- 첫 번째 인수로는 어떠한 값을 반환하는 생성 함수를, 두 번째 인수로는 해당 함수가 의존하는 값의 배열을 전달한다. useMemo는 렌더링 발생 시 의존성 배열의 값이 변경되지 않았으면 함수를 재실행하지 않고 이전에 기억해 둔 해당 값을 반환하고, 의존성 배열의 값이 변경됐다면 첫 번째 인수의 함수를 실행한 후에 그 값을 반환하고 그 값을 다시 기억해 둘 것이다.

#### 3.1.4 useCallback

- useMemo가 값을 기억했다면, **useCallback은 인수로 넘겨받은 콜백 자체를 기억한다.** 쉽게 말해 useCallback은 특정 함수를 새로 만들지 않고 다시 재사용한다는 의미다.

```js
const Childcomponent = memo(({ name, value, onChange }) => {
    // 렌더링이 수행되는지 확인하기 위해 넣었다.
    useEffect(() => {
        console.log('rendering!', name)
    })
    return (
        <>
        <hl>
        {name} {value ? '켜짐' : '꺼짐'}
        </hl>
        <button onClick={onChange}>toggle</button>
        </>
    )
})

function App() {
    const [status!, setStatusl] = useState(false)
    const [status2, setStatus2] = useState(false)

    const togglel =()=>{
        setStatusl(!statusl)
    }
    const toggle2 =()=>{
        setStatus2(!status2)
    }
    return (
        <>
        <ChildComponent name="l" value={statusl} onChange={togglel} />
        <ChildConiponent name="2" value={stati』s2} onChange={toggle2} />
        </>
    )
}
```

- memo를 사용해서 컴포넌트를 메모이제이션했지만 App의 자식 컴포넌트 전체가 렌더링되고 있다.
- 위 코드는 Childcomponent에 memo를 사용해 name, value, onChange의 값을 모두 기억하고, 이 값이 변경되지 않았을 때는 렌더링되지 않도록 작성된 코드다. **정상적인 흐름이라면 하나의 value 변경이 다른 컴포넌트에 영향을 미쳐서는 안 되고, 클릭할 때마다 하나의 컴포넌트만 렌더링되어야 한다.** 그러나 어느 한 버튼을 클릭하면 클릭한 컴포넌트 외에도 클릭하지 않은 컴포넌트도 렌더링되는 것을 알 수 있다.
- **그 이유는 state 값이 바뀌면서 App 컴포넌트가 리렌더링되고, 그때마다 매번 onChange로 넘기는 함수가 재생성되고 있기 때문이다.**

```js
const toggle1 = useCallback(
    function toggle1() {
        setStatusl(!status1)
    },
    [status1],
)
const toggle2 = useCallback(
    function toggle2() {
        setStatus2(!status2)
    },
    [status2],
)
```

- 값의 메모이제이션을 위해 useMemo를 사용했다면, 함수의 메모이제이션을 위해 사용하는 것이 useCallback이다. useMemo와 마찬가지로 의존성 배열이 변경되지 않는 한 함수를 재생성하지 않는다. **useCallback을 추가하면 해당 의존성이 변경됐을 때만 함수가 재생성된다.**

#### 3.1.5 useRef

- useRef는 usestate와 동일하게 컴포넌트 내부에서 렌더링이 일어나도 변경 가능한 상태값을 저장한다는 공통점이 있다. 그러나 usestate와구별되는 큰 차이점 두 가지를 가지고 있다.
    - useRef는 반환값인 객체 내부에 있는 current로 갮에 접근 또는 변경할 수 있다.
    - useRef는 그 값이 변하더라도 렌더링을 발생시키지 않는다.
- useRef에 대해 본격적으로 알아보기 전에 useRef가 왜 필요한지 먼저 고민해보자. **렌더링에 영향을 미치지않는 고정된 값을 관리하기 위해서 useRef를 사용한다면 useRef를 사용하지 않고 그냥 함수 외부에서 값을 선언해서 관리하는 것도 동일한 기능을 수행할 수도 있지 않을까?** 다음 예제를 보자.

```js
let value = 0
function Component() {
    function handleClick() {
        value += 1
    }

    // ...
}
```

- 결론부터 이야기하자면 이 방식은 몇 가지 단점이 있다.
- **먼저 컴포넌트가 실행되어 렌더링되지 않았음에도 value라는 값이 기본적으로 존재하게 된다.** 이는 메모리에 불필요한 값을 갖게 하는 악영향을 미친다. 그리고 만약 Component, 즉 **컴포넌트가 여러 번 생성된다면 각 컴포넌트에서 가리키는 값이 모두 value로 동일하다.** 컴포넌트가 초기화되는 지점이 다르더라도 하나의 값을 봐야 하는 경우라면 유효할 수도 있지만 대부분의 경우에는 컴포넌트 인스턴스 하나당 하나의 값을 필요로 하는 것이 일반적이다.
- useRef의 가장 일반적인 사용 예는 바로 DOM에 접근하고 싶을 때 일 것이다.
- useRef는 최초에 넘겨받은 기본값을 가지고 있다. 한 가지 명심할 것은 **useRef의 최초 기본값은 return 문에 정의해 둔 DOM이 아니고 useRef()로 넘겨받은 인수라는 것이다.** useRef가 선언된 당시에는 아직 컴포넌트가 렌더링되기 전이라 return으로 컴포넌트의 DOM이 반환되기 전이므로 undefined다.

#### 3.1.6 useContext

- usecontext에 대해 이해하려면 먼저 리액트의 Context에 대해 알아야 한다.

<h5>Context란?</h5>

- 리액트 애플리케이션은 기본적으로 부모 컴포넌트와 자식 컴포넌트로 이뤄진 트리 구조를 갖고 있기 때문에 부모가 가지고 있는 데이터를 자식에서도 사용하고 싶다면 props로 데이터를 넘겨주는 것이 일반적이다. 그러나 **전달해야 하는 데이터가 있는 컴포넌트와 전달받아야 하는 컴포넌트의 거리가 멀어질수록 코드는 복잡해진다.**
- prop 내려주기는 해당 데이터를 제공하는 쪽이나 사용하는 쪽 모두에게 불편하다. 해당 값을 사용하지 않는 컴포넌트에서도 단순히 값을 전달하기 위해 props가 열려 있어야 하고, 사용하는 쪽도 이렇게 prop 내려주기가 적용돼 있는지 확인해야 하는 등 매우 번거로운 작업이다.
- prop 내려주기를 극복하기 위해 등장한 개념이 바로 콘텍스트(Context)다. 콘텍스트를 사용하면, 이러한 명시적인 props 전달 없이도 선언한 하위 컴포넌트 모두에서 자유롭게 원하는 값을 사용할 수 있다. 

<h5>Context를 함수형 컴포넌트에서 사용할 수 있게 해주는 useContext 혹</h5>

```js
const Context = createContext<{ hello: string } | undefined>()
function ParentComponent() {
    return (
        <>
            <Context.Provider value={{ hello: 'react' }}>
                <Context.Provider value={{ hello: 'javascript' }}>
                    <ChiIdComponent />
                </Context.Provider〉
            </Context.Provider〉
        </>
    )
}

function ChildComponent() {
    const value = useContext(Context)
    // react가 아닌 javascript가 반환된다.
    return <>{value ? value.hello : ''}</>
}
```

- usecontext는 상위 컴포넌트에서 만들어진 Context를 함수형 컴포넌트에서 사용할 수 있도록 만들어진 훅이다. useContext를 사용하면 상위 컴포넌트 어딘가에서 선언된 <Context.Provider />에서 제공한 값을 사용할 수 있게 된다. 만약 여러 개의 Provider가 있다면 가장 가까운 Provider의 값을 가져오게 된다.

```js
function useMyContext() {
    const context = useContext(MyContext)
    if (context === undefined) {
        throw new Error(
        'useMyContext는 Contextprovider 내부에서만 사용할 수 있습니다.',
        )
    }
    return context
}
```

- 다수의 Provider와 useContext를 사용할 때. 특히 타입스크립트를 사용하고 있다면 위와 같이 별도 함수로 감싸서 사용하는 것이 좋다. 타입 추론에도 유용하고, 상위에 Provider가 없는 경우에도 사전에 쉽게 에러를 찾을 수 있다.

<h5>useContext를 사용할 때 주의할 점</h5>

- **usecontext를 함수형 컴포넌트 내부에서 사용할 때는 항상 컴포넌트 재활용이 어려워진다는 점을 염두에 둬야 한다.** usecontext가 선언돼 있으면 Provider에 의존성을 가지고 있는 셈이 되므로 아무데서나 재활용하기에는 어려운 컴포넌트가 된다. 해당 함수형 컴포넌트가 Provider 하위에 있지 않은 상태로 useContext를 사용한다면 예기치 못한 작동 방식이 만들어진다. **즉, usecontext가 있는 컴포넌트는 그 순간부터 눈으로는 직접 보이지도 않을 수 있는 Provider와의 의존성을 갖게 되는 셈이다.**
- 이러한 상황을 방지하려면 usecontext를 사용하는 컴포넌트를 최대한 작게 하거나 혹은 재사용되지 않을 만한 컴포넌트에서 사용해야 한다. **이러한 문제를 방지하기 위해 모든 콘텍스트를 최상위 루트 컴포넌트에 넣는 것은 어떨까?**
- 앞서 언급한 에러는 줄어들 수 있지만 리액트 애플리케이션 관점에서는 그다지 현명한 접
근법이 아니다. **콘텍스트가 많아질수록 루트 컴포넌트는 더 많은 콘텍스트로 둘러싸일 것이고 해당 props를 다수의 컴포넌트에서 사용할 수 있게끔 해야 하므로 불필요하게 리소스가 낭비된다. 따라서 컨택스트가 미치는 범위는 필요한 환경에서 최대한 좁게 만들어야 한다.**
- 마지막으로 **일부 리액트 개발자들이 콘텍스트와 useContext를 상태 관리를 위한 리액트의 API로 오해하고 있다는 것이다. 엄밀히 따지면 콘텍스트는 상태를 주입해 주는 API다.** 상태 관리 라이브러리가 되기 위해서 는 최소한 다음 두 가지 조건을 만족해야 한다.
    - 1) 어떠한 상태를 기반으로 다른 상태를 만들어 낼 수 있어야 한다.
    - 2) 필요에 따라 이러한 상태 변화를 최적화할 수 있어야 한다.
- 그러나 콘텍스트는 둘 중 어느 것도 하지 못한다. 단순히 props 값을 하위로 전달해 줄 뿐, usecontext를 사용한다고 해서 렌더링이 최적화되지는 않는다. 

```js
const MyContext = createContext<{ hello: string } | undefined>(undefined)
function ContextProvider({
    children,
    text,
}: PropsWith아ildren<{ text: string }>) {
    return (
        <MyContext.Provider value={{ hello： text }}>{children}</MyContext.Provider>
    )
}
function useMyContext() {
    const context = useContext(MyContext)
    if (context === undefined) {
        throw new Error(
        'useMyContext는 Contextprovider 내부에서만 사용할 수 있습니다.',
        )
    }
    return context
}

function GrandChildComponent() {
    const { hello } = useMyContextO
    useEffect(() => {
        console.log('렌더링 GrandChildComponent')
    })
    return <h3>{hello}</h3>
}

function ChildComponent() {
    useEffect(() => {
        console.log('렌더링 Childcomponent')
    })
    return <GrandChildConiponent />
}

function ParentComponent() {
    const [text, setText] = useStateC('')
    function handleChange(e: ChangeEvent<HTMLInpiitElement>) {
        setText(e.target.value)
    }
    useEffect(() => {
        console.log('렌더링 Parentcomponent‘)
    })

```

- ParentComponent에서 Provider의 값을 내려주고, 이를 useContext로 GrandChild
Component에서 사용 중이다. **언뚯 보기에는 text가 변경되는 Parentcomponent와 이를 사용하는 Grand아lildComponent만 렌더링될 것 같지만 그렇지 않다. 사실은 컴포넌트 트리 전체가 리렌더링되고 있다.**
- **부모 컴포넌트가 렌더링되면 하위 컴포넌트는 모두 리렌더링되기 때문이다.** usecontext는 상태를 관리하는 마법이 아니라는 사실을 반드시 기억해야 한다. 거듭 이야기하지만 **콘텍스트는 단순히 상태를 주입할 뿐 그 이상의 기능도, 그 이하의 기능도 하지 않는다.**
- 그렇다면 아래의 예제를 최적화하려면 어떻게 해야 할까? **예제에서 Childcomponent가 렌더링되지 않게 막으려면 React.memo를 써야 한다. memo는 props 변화가 없으면 리렌더링되지 않고 계속해서 같은 결과물을 반환할 것이다.**

#### 3.1.8 useImperativeHandle

- useimperativ애andle은 실제 개발 과정에서는 자주 볼 수 없는 훅으로 널리 사용되지 않는다. 그럼에도 uselmperativ애andle은 일부 사용 사례에서 유용하게 활용될 수 있다. useimperativ해andle을 이해하기 위해서는 먼저 React.forwardRef에 대해 일아야 한다.

<h5>forwardRef 살펴보기</h5>

- ref는 useRef에서 반환한 객체로, 리액트 컴포넌트의 props인 ref에 넣어 HTMLElement에 접근하는 용도로 흔히 사용된다. key와 마찬가지로 ref도 리액트에서 컴포넌트의 props로 사용할 수 있는 예약어로서 별도로 선언돼 있지 않아도 사용할 수다.
- **만약 이러한 ref를 상위 컴포넌트에서 하위 컴포넌트로 전달하고 싶다면 어떻게 해야 할까?** 즉, 상위 컴포넌트에서는 접근하고 싶은 ref가 있지만 이를 직접 props로 넣어 사용할 수 없을 때는 어떻게 해야 할까? 우리가 알고 있는 단순한 ref와 props에 대한 상식으로 이 문제를 해결한다면 다음 코드와 같은 결과물이 나올 것이다.

```js
function ChildComponent({ ref }) {
    useEffect(() => {
        // undefined
        console.log(ref)
    }, [ref])
    return <div>안녕!</div>
}

function ParentComponent() {
    const inputRef = useRef()
    return (
        <>
        <input ref={inputRef} />
        {/* 'ref' is not a prop. Trying to access it will result in 'undefined' being returned. If you
        need to access the same value within the child component, you should pass it as a different prop */}
        〈ChildComponent ref={inputRef} />
        </>
    )
}
```

- 리액트에서 ref는 props로 쓸 수 없다는 경고문과 함께 접근을 시도할 경우 undefined를 반환한다고 돼 있다. 그렇다면 예약어로 지정된 ref 대신 다른 props로 받으면 어떨까?

```js
function ChildComponent({ parentRef }) {
    useEffect(() => {
        // {current; undefined}
        // {current: HTMLInputElement}
        console.log(parentRef)
    }, [parentRef])
    return <div>안녕! </div>
}

function ParentComponent() {
    const inputRef = useRef()
        return (
            <>
            <input ref={inputRef} />
            <ChildComponent parentRef={inputRef} />
            </>
        )
}
```

- 이러한 방식은 앞선 예제와 다르게 잘 작동하는 것으로 보인다. 그리고 이는 클래스형 컴포넌트나 함수형 컴포넌트에서도 동일하게 작동한다. forwardRef는 방금 작성한 코드와 동일한 작업을 하는 리액트 API다. 그런데 단순히 이렇게 props로 구현할 수 있는 것을 왜 만든 것일까?
- **그럼에도 forwardRef가 탄생한 배경은 ref를 전달하는 데 있어서 일관성을 제공하기 위해서다.** 어떤 props 명으로 전달할지 모르고, 이에 대한 완전한 네이밍의 자유가 주어진 props보다는 forwardRef를 사용하면 좀 더 확실하게 ref를 전달할 것임을 예측할 수 있고, 또 사용하는 쪽에서도 확실히 안정적으로 받아서 사용할수 있다. forwardRef를 사용하는 다음 예제를 보자.

```js
const Childcomponent = forwardRef((props, ref) => {
    useEffect(() => {
        // {current: undefined}
        // {current: HTMLInputElement}
        console.log(ref)
    }, [ref])
return <div>안녕!</div>
})

function ParentComponent() {
    const inputRef = useRef()
    return (
        <>
        <input ref={inputRef} />
        <ChildComponent ref={inputRef} />
        </>
    )
}
```

- 먼저 ref를 받고자 하는 컴포넌트를 forwardRef로 감싸고, 두 번째 인수로 ref를 전달받는다. 그리고 부모컴포넌트에서는 동일하게 props, ref를 통해 ref를 넘겨주면 된다. 이렇게 forwardRef를 사용하는 코드로 수정하면 ref를 props로 전달할 수 있고, 전달받은 컴포넌트에서도 ref라는 이름을 그대로 사용할 수 있다.

<h5>useImperativeHandle이란?</h5>

- forwardRef에 대해 알아봤으니 useImperatiwHandle에 대해 살펴보자. **useImperativeHandle은 부모에게서 넘겨받은 ref를 원하는 대로 수정할 수 있는 훅이다.** 다음 코드를 보자.

```js
const Input = forwardRef((props, ref) => {
    // useImperativeHandle을 사용하면 ref의 동작을 추가로 정의할 수 있다.
    useImperativeHandle(
        ref,
        () => ({
            alert： () => alert(props.value),
        }),
        // useEffect의 deps와 같다.
        [props.value].
    )
    return <input ref={ref} {...props} />
})
```

- **‘useImperatiwHandle을 사용하면 부모 컴포넌트에서 노출되는 값을 원하는 대로 바꿀 수 있다’라는 말의 뜻이 명확해졌다.** 원래 ref는 {current: HTMLElement>}와 같은 형태로 HTMLElement만 주입할 수 있는 객체였다. 그러나 여기서는 전달받은 ref에다 useimperativ애andle 훅을 사용해 추가적인 동작을 정의했다. **이로써 부모는 단순히 HTMLElement뿐만 아니라 자식 컴포넌트에서 새롭게 설정한 객체의 키와 값에 대해서도 접근할 수 있게 됐다.** useImperatiwHandle을 사용하면 이 ref의 값에 원하는 값이나 액션을 정의할 수 있다.

#### 3.1.9 useLayoutEffect

- 공식 문서에 따르면 useLayoutEffect를 다음과 같이 정의하고 있다.
    - 이 함수의 시그니처는 useEffect와 동일하나, 모든 DOM의 변경 후에 동기적으로 발생핸다.
- 여기서 useLayoutEffect를 이해하기 위한 중요한 사실은 ‘모든 DOM의 변경 후에 useLayoutEffect의 콜백 함수 실행이 동기적으로 발생’한다는 점이다. **여기서 말하는 DOM 변경이란 렌더링이지, 브라우저에 실제로 해당 변경 사항이 반영되는 시점을 의미하는 것은 아니다.** 즉, 실행 순서는 다음과 같다.
    - 1) 리액트가 DOM을업데이트
    - 2) useLayoutEffect를실행
    - 3) 브라우저에 변경 사항을 반영
    - 4) useEffect를실행
- 그리고 동기적으로 발생한다는 것은 리액트는 useLayoutEffect의 실행이 종료될 때까지 기다린 다음에 화면을 그린다는 것을 의미한다. 즉, 리액트 컴포넌트는 useLayoutEffect가 완료될 때까지 기다리기 때문에 컴포넌트가 잠시 동안 일시 중지되는 것과 같은 일이 발생하게 된다. 따라서 이러한 작동 방식으로 인해 웹 애플리케이션 성능에 문제가 발생할 수 있다.
- 그럼 언제 useLayoutEffect를 사용하는 것이 좋을까? useLayoutEffect의 특징상 DOM은 계산됐지만 이것이 화면에 반영되기 전에 하고 싶은 작업이 있을 때와 같이 반드시 필요할 때만 사용하는 것이 좋다.

#### 3.1.11 훅의 규칙

- 리액트에서 제공하는 훅은 사용하는 데 몇 가지 규칙이 존재한다. 이러한 규칙을 rules-of-hooks라고 하며 이와 관련된 ESLint 규칙인 react-hooks/r니les-of-hooks도 존재한다.
    - 1) **최상위에서만 훅을 호출해야 한다.** 반복문이나 조건문, 중첩된 함수 내에서 훅을 실행할수 없다. **이규칙을 따라야만 컴포넌트가 렌더링될 때마다 항상 동일한 순서로 훅이 호출되는 것을 보장할 수 있다.**
    - 훅을 호출할 수 있는 것은 리액트 함수형 컴포넌트. 혹은 사용자 정의 훅의 두 가지 경우뿐이다. 일반 자바스크립트 함수에 서는 훅을 사용할 수 없다.


### 3.2 사용자 정의 혹과 고차 컴포넌트 중 무엇을 써야 할까?

- 개발자라면 누구나 중복 코드를 피해야 한다는 말에 대해 십분 공감할 것이다. 같은 작업을 하는 같은 내용의 코드가 있다는 사실은 코드의 존재만으로도 비효율이며 유지보수도 어렵게 만든다. 일반적인 자바스크립트에서 재사용 로직을 작성하는 방식 외에도 리액트에서는 재사용할 수 있는 로직을 관리할 수 있는 두 가지 방법이 있다. 
- 바로 사용자 정의 훅(custom hook)과 고차 컴포넌트(higher order component)다. 사용자 정의 훅과 고차 컴포넌트가 무엇이며 어떻게 쓰는지, 공통된 코드를 하나로 만들고자 할 때 어떤 것을 선택해야
하는지를 살펴보자.

#### 3.2.1 사용자 정의 훅

- **서로 다른 컴포넌트 내부에서 같은 로직을 공유하고자 할 때 주로 사용되는 것이 바로 사용자 정의 훅이다.** 뒤이어 사용할 고차 컴포넌트는 굳이 리액트가 아니더라도 사용할 수 있는 기법이지만 사용자 정의 훅은 리액트에서만 사용할 수 있는 방식이다. **이 사용자 정의 훅은 3.1 절 ‘리액트의 모든 훅 파헤치기’에서 소개한 훅을 기반으로 개발자가 필요한 훅을 만드는 기법이다.** 이 사용자 정의 훅의 규칙 중 하나는 이름이 반드시 use로 시작하는 함수를 만들어야 한다는 것이다.  리액트 훅의 이름은 use로 시작한다는 규칙이 있으며, 사용자 정의 훅도 이러한 규칙을 준수함으로써 개발 시 해당 함수가 리액트 훅이라는 것을 바로 인식할 수 있다는 장점도 있다.

```js
import { useEffect, useState } from 'react'
// HTTP 요청을 하는 사용자 정의 훅
function useFetch<T>(
url: string,
{ method, body }: { method: string; body?: XHLHttpRequestBodylnit },
( {
    // 응답 결과
    const [result, setResult] = useState<T | undefined>()
    // 요청 중 여부
    const [isLoading, setlsLoading] = useState<boolean>(false)
    // 2xx 3xx로 정상 응답인지 여부
    const [ok, setOk] = useState<boolean | undefined>()
    // HTTP status
    const [status, setStatus] = useState<number | undefined>()
    useEffect(() => {
        const abortControUer = new AbortControUer()
        ;(async () => {
            setlsLoading(true)
            const response = await fetch(url, {
                methodj
                body,
                signal: abortcontroller.signal,
            })
            setOk(response.ok)
            setStatus(response.status)
            if (response.ok) {
                const apiResult = await response.json()
                setResult(apiRes냐It)
            }
            setlsLoading(false)
        })()
        return () => {
        abortcontroller.abort()
        }
    }, [url, method, body])
    return { ok, result, isLoading, status }
}
```

- 이 코드는 fetch를 이용흐H API> 호출하는 로직을 사용자 정의 훅으로 분리한 예제다. **만약 훅으로 분리하지 않았다면 fetch로 API 호출을 해야 하는 모든 컴포넌트 내에서 공통적으로 관리되지 않는 최소 4개의 state를 선언해서 각각 구현했어야 할 것이다.**
- 이렇게 복잡하고 반복되는 로직은 사용자 정의 훅으로 간단하게 만들 수 있다. **훅에서 필요한 usestate와 useEffect 로직을 사용자 정의 훅인 useFetch 내부에 두면 사용하는 쪽에서는 useFetch 훅만 사용해도 손쉽게 중복되는 로직을 관리할 수 있다.**
- 이 코드를 통해 왜 use라는 이름을 지켜야 하는지 알 수 있게 됐다. 사용자 정의 훅은 내부에 usestate와 useEffect 등을 가지고 자신만의 원하는 훅을 만드는 기법으로, 내부에서 useState와 같은 리액트 훅을 사용하고 있기 때문에 당연히 앞서 언급한 리액트 훅의 규칙을 따라야 한다. 그리고 이 리액트 훅의 규칙을 따르고 react-hooks/rules-of-hooks의 도움을 받기 위해서는 use로 시작하는 이름을 가져야 한다. 만약 그렇지 않으면 에러가 발생한다.

#### 3.2.2 고차 컴포넌트

- 고차 컴포넌트(HOC, Higher Order Component)는 컴포넌트 자체의 로직을 재사용하기 위한 방법이다. 사용자 정의 훅은 리액트 훅을 기반으로 하므로 리액트에서만 사용할 수 있는 기술이지만 **고차 컴포넌트는 고차 함수(Higher Order Function)의 일종으로, 자바스크립트의 일급 객체, 함수의 특징을 이용하므로 굳이 리액트가 아니더라도 자바스크립트 환경에서 널리 쓰일 수 있다.**
- 리액트에서는 이러한 고차 컴포넌트 기법으로 다양한 최적화나 중복 로직 관리를 할 수 있다. **리액트에서 가장 유명한 고차 컴포넌트는 리액트에서 제공하는 API 중 하나인 React.memo다.**

<h5>React.memo란?</h5>

- React.memo를 이해하려면 먼저 앞서 살펴본 렌더링에 대해 다시금 떠올릴 필요가 있다. 리액트 컴포넌트가 렌더링하는 조건에는 여러 가지가 있지만 그중 하나는 바로 부모 컴포넌트가 **새롭게 렌더링될 때다. 이는 자 식 컴포넌트의 props 변경 여부와 관계없이 발생한다. 다음 코드를 보자.**

```js
const Childcomponent = ({ value }: { value: string }) => {
    useEffect(() => {
        console.log(' 렌더링 !')
    })
    return <>안녕하세요! {value}</>
}

function ParentComponent() {
    const [state, setState] = useState(l)
    function handleChange(e: ChangeEvent<HTMLInputEleinent>) {
        setState(Number(e.target.value))
    }
    return (
    <>
    <input type="number" value={state} onChange={handleChange} />
    <ChildComponent value="hello" />
    </>
    )
}
```

- 예제에서 Childcomponent는 props인 value="hello"가 변경되지 않았음에도 handleChange로 인해 setstate를 실행해 state를 변경하므로 리렌더링이 발생한다.
- 이처럼 props의 변화가 없음에도 컴포넌트의 렌더링을 방지하기 위해 만들어진 리액트의 고차 컴포넌트가 바로 React.memo다. **React.memo는 렌더링하기에 앞서 props를 비교해 이전과 props가 같다면 렌더링 자체를 생략하고 이전에 기억해 둔(memoization) 컴포넌트를 반환한다.** 앞선 예제를 memo로 감싸서 다시 실행해 보자.

```js
const Childcomponent = memo(({ value }: { value： string }) => {
    useEffect(() => {
        console.log('렌더링!')
    })
    return <>안녕하세요! {value}</>
})
```

- 이제 Parentcomponent에서 아무리 state가 변경돼도 ChildComponent는 다시 렌더링되지 않는다. 그 이유는 props가 변경되지 않았고, 변경되지 않았다는 것을 히emo가 확인하고 이전에 기억한 컴포넌트를 그대로 반환한 것이다. 결국 앞서서 발생했던 불필요한 렌더링 작업을 생략할 수 있게 됐다.

<h5>고차 함수 만들어보기</h5>

- 리액트의 고차’ 컴포넌트를 만들기에 앞서 먼저 자바스크립트에서 고차" 함수를 만드는 것에 대해 살펴보고자 한다. 리액트의 함수형 컴포넌트도 결국 함수이기 때문에 함수를 기반으로 고차 함수를 만드는 것을 먼저 이해해야 한다. **고차 함수의 사전적인 정의를 살펴보면 ‘함수를 인수로 받거나 결과로 반환하는 함수’라고 정의돼 있다. 가장 대표적인 고차 함수로는 리액트에서 배열을 렌더링할 때 자주 사용하는 Array.prototype.map을 들 수 있다.** 다음 예제를 통해 고차 함수가 무엇인지 알아보자.

```js
const list = [1, 2, 3]
const doubledList = list.map((item) => item * 2)
```

- Array.prototype.map을 사용하는 예제를 살펴보면 앞서 고차 함수의 사전적 정의와 마찬가지로 (item) => item * 2, 즉 함수를 인수로 받는다는 것을 알 수 있다. 비롯해 forEach나 reduce 등도 고차 함수임을 알 수 있다. 이번에는 리액트 코드에서 살펴보자.

```js
// 즉시 실행 함수로 setter를 만든다.
const setstate = (function () {
    // 현재 index를 클로저로 가둬놔서 이후에도 계속해서 동일한 index에
    // 접근할 수 있도록 한다.
    let currentindex = index
    return function (value) {
        global.states[currentlndex] = value
        // 컴포넌트를 렌더링한다. 실제로 컴포넌트를 렌더링하는 코드는 생략했다.
    }
})()
```

- 위 예제는 앞서 3.1 절 ‘리액트의 모든 훅 파헤치기’에서 설명한 setstate 함수를 구현한 예제다. 이 setState는 usestate에서 반환된 두 번째 배열의 값으로 실행할 수 있는 함수를 반환한다. 이 역시 마찬가지로 함수를 결과로 반환하는’이라는 조건을 만족하므로 고차 함수라고 할 수 있다.
- 이 번에는 직접 고차 함수를 만들어 보자. 다음은 두 값을 더하는 함수를 고차 함수로 구현해 보았다.

```js
function add(a) {
    return function (b) {
        return a + b
    }
}

const result = add(1) // 여기서 result는 앞서 반환한 함수를 가리킨다.
const result2 = result(2) // 비로소 a와 b룔 더한 3이 반환된다.
```

- add(l)라는 함수를 호출하는 시점에 1이라는 정보가 a에 포함되고, 이러한 정보가 담긴  수를 result로 반환된다. 잠깐, 이것은 마치 usestate의 원리와 비슷하다. useState의 실행은 함수 호출과 동시에 끝났지만 state의 값은 별도로 선언한 환경, 즉 클로저에 기억된다. 여기에서도 마찬가지로 a=l이라는 정보가 담긴 클로저가 result에 포함됐고, result(2)를 호출하면서 이 클로저에 담긴 a=l인 정보를 활용해 1 + 2의 결과를 반환할 수 있게 됐다.

<h5>고차 함수를 활용한 리액트 고차 컴포넌트 만들어보기</h5>

- 사용자 인증 정보에 따라서 인증된 사용자에게는 개인화된 컴포넌트를, 그렇지 않은 사용자에게는 별도로 정의된 공통 컴포넌트를 보여주는 시나리오를 떠올려보자. 고차 함수의 특징에 따라 개발자가 만든 또 다른 함수를 반환할 수 있다는 점에서 고차 컴포넌트를 사용하면 매우 유용하다. 다음 예제를 보자.

```js
interface LoginProps {
    loginRequired?: boolean
}
function withLoginComponent<T>(Component: ComponentType<T>) {
    return function (props; T & LoginProps) {
        const { loginRequired, ...restProps } = props
        if (loginRequired) {
            return <>로그인이 필요합니다.</>
        }
        return <Component {...(restProps as T)} />
    }
}

// 원래 구현하고자 하는 컴포넌트를 만들고, WithLoginComponent로 감싸기만 하면 끝이다.
// 로그인 여부, 로그인이 안 되면 다른 컴포넌트를 렌더링하는 책임은 모두
// 고차 컴포넌트인 WithLoginComponent에 맡길 수 있어 매우 편리하다.
const Component = withLoginComponent((props: { value: string }) => {
    return <h3>{props.value}</h3>
})

export default function App() {
    // 로그인 관련 정보를 가져온다.
    const isLogin = true
    return <Component value="text" loginRequired={isLogin} />
    // return <Component value="text" />;
}
```

- Component는 우리가 아는 일반적인 함수형 컴포넌트와 같은 평범한 컴포넌트지만, 이 함수 자체를 withLoginComponent라 불리는 고차 컴포넌트로 감싸뒀다. withLoginComponent는 함수(함수형 컴포넌트)를 인수로 받으며. 컴포넌트를 반환하는 고차 컴포넌트다.
- **이처럼 고차 컴포넌트는 컴포넌트 전체를 감쌀 수 있다는 점에서 사용자 정의 훅보다 더욱 큰 영향력을 컴포넌트에 미칠 수 있다. 단순히 값을 반환하거나 부수 효과를 실행하는 사용자 정의 훅과는 다르게, 고차 컴포넌트는 컴포넌트의 결과물에 영향을 미칠 수 있는 다른 공통된 작업을 처리할 수 있다.**
- 이번에는 고차 컴포넌트를 구현하기에 앞서 구현 시 주의할 점을 살펴보자. **사용자 정의 훅이 use로 시작하는 이름을 사용했다면 리액트의 고차 컴포넌트도 마찬가지로 with로 시작하는 이름을 사용해야 한다는 것이다.** 이는 앞선 use의 경우와 같이 ESLint 규칙 등으로 강제되는 사항은 아니지만 리액트 라우터의 withRouter와 같이 리액트 커뮤니티에 널리 퍼진 일종의 관습이라 볼 수 있다. with가 접두사로 붙어 있으면 고차 컴포넌트임을 손쉽게 알아채어 개발자 스스로가 컴포넌트 사용에 주의를 기울일 수 있으므로 반드시 with로 시작하는 접두사로 고차 컴포넌트를 만들자.
- **고차 컴포넌트를 사용할 때 주의할 점 중 하나는 부수 효과를 최소화해야 한다는 것이다.** 고차 컴포넌트는 반드시 컴포넌트를 인수로 받게 되는데, 반드시 컴포넌트의 props를 임의로 수정, 추가, 삭제하는 일은 없어야 한다. 앞의 예제의 경우에는 loginRequired라는 props를 추가했을 뿐, 기존에 인수로 받는 컴포넌트의 props는 건드리지 않았다. 만약 기존 컴포넌트에서 사용하는 props를 수정하거나 삭제한다면 고차 컴포넌트를 사용하는 쪽에서는 언제 props가 수정될지 모른다는 우려를 가지고 개발해야 하는 불편함이 생긴다. . 만약 컴포넌트에 무언가 추가적인 정보를 제공해 줄 목적이라면 별도 props로 내려주는 것이 좋다.
- **마지막으로 주의할 점은, 여러 개의 고차 컴포넌트로 컴포넌트를 감쌀 경우 복잡성이 커진다는 것이다.** 다음 예제 코드를 보자.

```js
const Component = withHigherOrderComponentl(
    withHigher0rderComponent2(
        withHigher0rderComponent3(
            withHigher0rderComponent4(
                withHigher0rderCoinponent5(() => {
                    return <>안녕하세요.</>
                })>
            ),
        ),
    ),
)
```

- 고차 컴포넌트가 컴포넌트를 또 다른 컴포넌트로 감싸는 구조로 돼 있다 보니, 여러 개의 고차 컴포넌트가 반복적으로 컴포넌트를 감쌀 경우 복잡성이 매우 커진다. 고차 컴포넌트가 증가할수록 개발자는 이것이 어떤 결과를 만들어 낼지 예측하기 어려워진다. 따라서 고차 컴포넌트는 최소한으로 사용하는 것이 좋다.

#### 3.2.3 사용자 정의훅과 고차 컴포넌트 중 무엇을 써야 할까?

- 사용자 정의 훅과 고차 컴포넌트 모두 리액트 코드에서 어떠한 로직을 공통화해 별도로 관리할 수 있다는 특징이 있다. 애플리케이션 전반에 필요한 중복된 로직을 별도로 분리해 컴포넌트의 크기를 줄이고 가독성을 향상시키는 데 도움을 줄 수 있다. 그렇다면 어떠한 경우에 각각 사용자 정의 훅 또는 고차 컴포넌트를 써야할까?

<h5>사용자 정의 훅이 필요한 경우</h5>

- **단순히 useEffect, usestate와 같이 리액트에서 제공하는 훅으로만 공통 로직을 격리할 수 있다면 사용자 정의 훅을 사용하는 것이 좋다.** 사용자 정의 훅은 그 자체로는 렌더링에 영향을 미치지 못하기 때문에 사용이 제한적이므로 반환하는 값을 바탕으로 무엇을 할지는 개발자에게 달려 있다. 따라서 컴포넌트 내부에 미치는 영향을 최소화해 개발자가 훅을 원하는 방향으로만 사용할 수 있다는 장점 이 있다. 

```js
// 사용자 정의 훅올 사용하는 경우
function HookComponent() {
    const { loggedln } = useLogin()

    useEffect(() => {
        if (!loggedln) {
        // do something..
        }
    }, [loggedln])
}
// 고차 컴포넌트를 사용하는 경우
const HOCComponent = withLoginComponent(() => {
// do something...
}
```

- 로그인 정보를 가지고 있는 훅인 useLogin은 단순히 loggedln에 대한 값만 제공할 뿐, 이에 대한 처리는 컴포넌트를 사용하는 쪽에서 원하는 대로 사용 가능하다. 따라서 부수 효과가 비교적 제한적이라고 볼 수 있다. 반면 WithLoginComponent는 고차 컴포넌트가 어떤 일을 하는지, 어떤 결과물을 반환할지는 고차 컴포넌트를 직접 보거나 실행하기 전까지는 알 수 없다. 대부분의 고차 컴포넌트는 렌더링에 영향을 미치는 로직이 존재하므로 사용자 정의 훅에 비해 예측하기가 어렵다.
- **따라서 단순히 컴포넌트 전반에 걸쳐 동일한 로직으로 값을 제공하거나 특정한 훅의 작동을 취하게 하고 싶다면 사용자 정의 훅을 사용하는 것이 좋다.**

<h5>고차 컴포넌트를 사용해야 하는 경우</h5>

- 앞선 예제와 같이 만약 로그인되지 않은 어떤 사용자가 컴포넌트에 접근하려 할 때 애플리케이션 관점에서 컴포넌트를 감추고 로그인을 요구하는 공통 컴포넌트를 노출하는 것이 좋을 수 있다. 혹은 에러 바운더리와 비슷하게 어떠한 특정 에러가 발생했을 때 현재 컴포넌트 대신 에러가 발생했음을 알릴 수 있는 컴포넌트를 노출하는 경우도 있을 것이다. 앞선 예제를 조금 변경해 보자.

```js
function HookComponent() {
    const { loggedln } = useLogin()
    if (!loggedln) {
        return <LoginComponent />
    }
    return <>안녕하세요.</>
}

const HOCComponent = withLoginComponent(() => {
    // loggedln state의 값을 신경 쓰지 않고 그냥 컴포넌트에 필요한 로직만
    // 추가해서 간단해졌다. loggedln state에 따른 제어는 고차 컴포넌트에서 해줄 것이다.
    return <>안녕하세요.</>
})
```

- 만약 이러한 작업을 사용자 정의 훅으로 표현해야 한다고 가정해 보자. 어차피 loggedln이 faIse인 경우에 렌더링해야 하는 컴포넌트는 동일하지만 사용자 정의 훅만으로는 이를 표현하기 어렵다. 사용자 정의 훅은 해당 컴포넌트가 반환하는 랜더링 결과물에까지 영향을 미치기는 어렵기 때문이다.
- **함수형 컴포넌트의 반환값, 즉 렌더링의 결과물에도 영향을 미치는 공통 로직이라면 고차 컴포넌트를 사용하자.** 고차 컴포넌트는 이처럼 공통화된 렌더링 로직을 처리하기에 매우 훌륭한 방법이다. 물론 앞서 이야기한 것처럼 고차 컴포넌트가 많아질수록 복잡성이 기하급수적으로 증가하므로 신중하게 사용해야 한다.

#### 3.2.4 정리

- 지금까지 사용자 정의 훅과 고차 컴포넌트가 무엇인지, 또 언제 사용하면 좋을지 살펴봤다. 개발하는 애플리케이션의 규모가 커지고, 처리해야 하는 로직이 많아질수록 중복 작업에 대한 고민 또한 필연적으로 많아질 수밖에 없다. 공통화하고 싶은 작업이 무엇인지, 또 현재 이를 처리해야 하는 상황을 잘 살펴보고 적절한 방법을 고른다면 애플리케이션 개발이 더 효율적으로 개선될 것이다.


<h3>끝!</h3>
