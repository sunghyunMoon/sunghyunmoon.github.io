---
title: 3장 리액트 훅 깊게 살펴보기
categories:
- React Deep Dive
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

### 3.1 리액트의 모든 훅 파헤치기

#### 3.1.1 useState

- useState는 함수형 컴포넌트 내부에서 상태를 정의하고, 이 상태를 관리할 수 있게 해주는 훅
-  함수형 컴포넌트는 매번 함수를 실행해 렌더링이 일어나고, 함수 내부의 값은 함수가 실행될 때마다 다시 초기화
- usestate 훅의 결괏값은 어떻게 함수가 실행돼도 그 값을 유지하고 있을까?
    - 리액트는 클로저를 이용
    - 클로저는 어떤 함수(usestate) 내부에 선언된 함수(setstate)가 함수의 실행이 종료된 이후에도(useState가 호출된 이후에도) 지역변수인 state를 계속 참조할 수 있다는 것을 의미
- useState에 변수 대신 함수를 넘기는 것을 게으른 초기화(lazy initialization)
    - 게으른 초기 함수는 오로지 state가 처음 만들어질 때만 사용

#### 3.1.2 useEffect

- useEffect의 정의를 정확하게 내리자면 useEffect는 애플리케이션 내 컴포넌트의 여러 값들을 활용해 동기적으로 부수 효과를 만드는 메커니즘

##### 클린없 함수의 목적

-  클린업 함수는 비록 새로운 값을 기반으로 렌더링 뒤에 실행되지만 이 변경된 값을 읽는 것이 아니라 함수가 정의됐을 당시에 선언됐던 이전 값을 보고 실행됨
- 클린업 함수는 언마운트라기보다는 함수형 컴포넌트가 리렌더링됐을 때 의존성 변화가 있었을 당시 이전의 값을 기준으로 실행되는, 말 그대로 이전 상태를 청소해 주는 개념으로 보는 것

#### 3.1.3 useMemo

- useMemo는 비용이 큰 연산에 대한 결과를 저장(메모이제이션)해 두고, 이 저장된 값을 반환하는 훅

#### 3.1.4 useCallback

-  useCallback은 인수로 넘겨받은 콜백 자체를 기억

#### 3.1.5 useRef

- useRef는 반환값인 객체 내부에 있는 current로 값에 접근 또는 변경할 수 있음
- useRef는 그 값이 변하더라도 렌더링을 발생시키지 않음

#### 3.1.6 useContext

- usecontext는 상위 컴포넌트에서 만들어진 Context를 하위 함수형 컴포넌트에서 사용할 수 있도록 만들어진 훅

#### 3.1.8 useImperativeHandle

##### forwardRef 살펴보기

- ref를 상위 컴포넌트에서 하위 컴포넌트로 전달하고 싶을때 사용

##### useImperativeHandle란?

- useImperativeHandle은 부모에게서 넘겨받은 ref를 원하는 대로 수정할 수 있는 훅
- useImperativeHandle을 사용하면 이 ref의 값에 원하는 값이나 액션을 정의할수 있음

#### 3.1.9 useLayoutEffect

- 이 함수의 시그니처는 useEffect와 동일하나, 모든 DOM의 변경 후에 동기적으로 발생
-  여기서 말하는 DOM 변경이란 렌더링이지, 브라우저에 실제로 해당 변경 사항이 반영되는 시점을 의미하는 것은 아님
- 동기적으로 발생한다는 것은 리액트는 useLayoutEffect의 실행이 종료될 때까지 기다린 다음에 화면을 그린다는 것을 의미

### 3.2 사용자 정의 혹과 고차 컴포넌트 중 무엇을 써야 할까?

-  리액트에서는 재사용할 수 있는 로직을 관리할 수 있는 두 가지 방법
- 바로 사용자 정의 훅(custom hook)과 고차 컴포넌트(higher order component)

#### 3.2.1 사용자 정의 혹

- 서로 다른 컴포넌트 내부에서 같은 로직을 공유하고자 할 때 주로 사용되는 것이 바로 사용자 정의 훅
- 사용자 정의 훅은 내부에 usestate와 useEffect 등을 가지고 자신만의 원하는 훅을 만드는 기법

#### 3.2.2 고차 컴포넌트

- 고차 컴포넌트(HOC, Higher Order Component)는 컴포넌트 자체의 로직을 재사용하기 위한 방법

##### 고차 함수 만들어보기

- 고차 함수의 사전적인 정의를 살펴보면 ‘함수를 인수로 받거나 결과로 반환하는 함수’라고 정의(예: Array.prototype.map)
- 단순히 값을 반환하거나 부수 효과를 실행하는 사용자 정의 훅과는 다르게, 고차 컴포넌트는 컴포넌트의 결과물에 영향을 미칠 수 있는 다른 공통된 작업을 처리할 수 있음

#### 3.2.3 사용자 정의훅과 고차 컴포넌트 중 무엇을 써야 할까?

- 사용자 정의 훅과 고차 컴포넌트 모두 리액트 코드에서 어떠한 로직을 공통화해 별도로 관리할 수 있다는 특징

##### 사용자 정의 훅이 필요한 경우

- 단순히 useEffect, usestate와 같이 리액트에서 제공하는 훅으로만 공통 로직을 격리할 수 있다면 사용자 정의 훅을 사용

##### 고차 컴포넌트를 사용해야 하는 경우

- 함수형 컴포넌트의 반환값, 즉 렌더링의 결과물에도 영향을 미치는 공통 로직이라면 고차 컴포넌트를 사용

<h3>끝!</h3>
