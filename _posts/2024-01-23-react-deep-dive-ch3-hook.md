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

<h3>끝!</h3>
