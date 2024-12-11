---
title: React Compound Component Pattern
categories:
- ToyProject
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

React Compound Component Pattern에 대해 공부해보자.

### 리액트 컴파운드 컴포넌트 패턴 소개

- 리액트(React)를 사용하다 보면, 한 컴포넌트 내에서 특정 기능을 수행하고, 그 기능과 밀접하게 연관된 하위 요소들을 함께 구성하는 상황을 자주 맞닥뜨리게 된다. 
- 이때 흔히 떠오르는 것은 "props drilling" 혹은 하위 컴포넌트에 일일이 필요한 값을 내려주는 패턴이다. 하지만 이런 방법은 컴포넌트 구조가 커지면서 점점 복잡해지고, 재사용성이나 가독성이 떨어질 수 있다. 이러한 문제를 해결하기 위한 한 가지 유용한 디자인 패턴이 바로 컴파운드(Compound) 컴포넌트 패턴이다.

### w/o 컴파운트 패턴

- 컴파운트 패턴을 적용하기 전 아래와 같은 코드가 있다고 가정해보자.
- 예를 들어, Select라는 컴포넌트가 있다. 이 컴포넌트는 셀렉트 박스를 열고/닫는 로직, 현재 선택된 값, 옵션 리스트 관리 등의 로직을 갖고 있다. 

```js
function App() {
  return (
    <Select />
  );
}
```

- 이 코드만 봐서는 Select 내부에 무엇이 들어 있는지, 어떻게 구성되어있는지 알 수 없다.


<h3>끝!</h3>
