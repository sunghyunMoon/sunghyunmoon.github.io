---
title: HOC 패턴
categories:
- React
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react_pattern.png"
---

종종 여러 컴포넌트에서 같은 로직을 사용해야 하는 경우가 있다. 이런 로직은 컴포넌트의 스타일시트를 설정하는 것일 수 있고, 권한을 요청하거나, 전역 상태를 추가하는 것일 수 있다.

같은 로직을 여러 컴포넌트에서 재사용하는 방법 중 하나로 고차 컴포넌트 패턴을 활용하는 방법이 있다. 이 패턴은 앱 전반적으로 재사용 가능한 로직을 여러 컴포넌트들이 쓸 수 있게 해 준다.

HOC 패턴에 대해 공부해보자.

### 고차 컴포넌트 소개

- **고차 컴포넌트(HOC, Higher Order Component)는 컴포넌트 자체의 로직을 재사용하기 위한 방법**이다. 사용자 정의 훅은 리액트 훅을 기반으로 하므로 리액트에서만 사용할 수 있는 기술이지만 고차 컴포넌트는 고차 함수(Higher Order Function)의 일종으로, 자바스크립트의 일급 객체, 함수의 특징을 이용하므로 굳이 리액트가 아니더라도 자바스크립트 환경에서 널리 쓰일 수 있다.
- 리액트에서는 이러한 고차 컴포넌트 기법으로 다양한 최적화나 중복 로직 관리를 할 수 있다. 고차 컴포넌트의 사전적 정의는 "컴포넌트를 인수로 받거나 결과로 반환하는 컴포넌트"이다.

## 고차 컴포넌트 만들기 (1)

- 여러 컴포넌트에게 동일한 스타일을 적용하고 싶다고 가정하자. 로컬 스코프에 style 객체를 직접 만드는 대신, HOC가 style 객체를 만들어 컴포넌트에게 전달하도록 한다.

```js
function withStyles(Component) {
  return props => {
    const style = { padding: '0.2rem', margin: '1rem' }
    return <Component style={style} {...props} />
  }
}

const Button = () = <button>Click me!</button>
const Text = () => <p>Hello World!</p>

const StyledButton = withStyles(Button)
const StyledText = withStyles(Text)
```

- 이 패턴에서는 기존 컴포넌트를 입력받아, 변경된(혹은 추가된) 기능을 가진 새로운 컴포넌트를 반환한다. 다시 말해, withStyles는 HOC로서, 기존 컴포넌트를 입력받아 스타일이 추가된 새 컴포넌트를 반환한다.
- 사용자 정의 훅이 use로 시작하는 이름을 사용했다면 **리액트의 고차 컴포넌트도 마찬가지로 with로 시작하는 이름을 사용해야 한다.** ESLint 규칙 등으로 강제되는 사항은 아니지만 리액트 라우터의 withRouter와 같이 리액트 커뮤니티에 널리 퍼진 일종의 관습이다.

## 고차 컴포넌트 만들기 (2)

- 데이터를 받아오는 중에는 "로딩 중..." 이라는 메시지를 화면에 보여주고 싶다. DogImages에 직접 기능을 구현하는 대신 고차 컴포넌트 패턴을 활용할 것이다.

```js
import React, { useEffect, useState } from "react";

// withLoader는 Element(컴포넌트)와 url(데이터를 불러올 주소)을 인자로 받고,
// 데이터 로딩 로직을 담은 새로운 컴포넌트를 반환하는 HOC이다.
export default function withLoader(Element, url) {
  return (props) => {
    const [data, setData] = useState(null);

    useEffect(() => {
      async function getData() {
        const res = await fetch(url);
        const data = await res.json();
        setData(data);
      }

      getData();
    }, [url]);

    // 데이터가 아직 로딩 중이라면 로딩 메시지를 표시
    if (!data) {
      return <div>Loading...</div>;
    }

    // 데이터 로딩이 완료되면, 원래의 Element 컴포넌트에 data를 props로 넘겨 렌더링
    return <Element {...props} data={data} />;
  };
}
```

- 특정 URL로부터 데이터를 로드한 뒤, 로딩 상태를 관리하고, 로드된 데이터를 원하는 컴포넌트에 주입하는 역할을 수행한다.
- 이를 통해 데이터 페칭 로직을 별도의 고차 컴포넌트로 분리하고, 실제 UI를 구현하는 컴포넌트에서는 데이터 처리 로직 없이 화면 렌더링에만 집중할 수 있게 된다.

<h3>끝!</h3>
