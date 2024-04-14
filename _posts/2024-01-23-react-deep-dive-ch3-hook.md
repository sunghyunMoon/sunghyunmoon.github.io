---
title: 3장 리액트 훅 깊게 살펴보기
categories:
- React Deep Dive
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

함수형 컴포넌트가 상태를 사용하거나 클래스형 컴포넌트의 생명주기 메서드를 대체하는 등의 다양한 작업을 하기 위해 훅(hook)이라는 것이 추가됐다. 훅을 활용하면 클래스형 컴포넌트가 아니더라도 리액트의 다
양한 기능을 활용할 수 있다. 리액트에서 현재 사용 가능한 훅이 무엇이고, 어떻게 쓰이는지, 그리고 훅을 사용할 때 주의할 점은 무엇인지 확인해 보자.

#### 3.1 리액트의 모든 훅 파헤치기

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

<h3>끝!</h3>
