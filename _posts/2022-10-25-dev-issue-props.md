---
title: 자식 컴포넌트의 state을 부모 컴포넌트와 함께 사용하기
categories:
- React
feature_image: "https://picsum.photos/2560/600?image=872"
---

자식 컴포넌트에서 text가 존재하는지를 나타내는 hasText라는 state을 들고 있었고, 이것은 onKeyUp 이벤트 핸들러를 통해 set되고 있었다. 그리고 이 hasText라는 state가 부모 컴포넌트도 필요했고, 이  state을 이용해 버튼 컨트롤을 disable해야 하는 이슈가 있었다...

#### 상황 정리

- View 단에서 **부모 컴포넌트와 자식 컴포넌트가 같은 state을 공유**해야하는 상황
- **해당 state은 자식 컴포넌트의 이벤트 핸들러를 통해 설정**됨

#### 이슈 해결

- 보통 부모 컴포넌트에서 자식 컴포넌트로 데이터를 전달할 때는 props를 통해 전달한다. 
- 그래서 기존의 자식 컴포넌트에 있던 useState 훅을 부모 컴포넌트로 옮기고, 부모 컴포넌트는 자식 컴포넌트에게 props로 hasText(state)와 setHasText(setter)를 전달했다.
- 예시는 아래와 같다.

```ts
import React, { useState } from 'react';

function App() {
  return (
    <div className="App">
      <Parent />
    </div>
  );
};

const Parent = (props) => {
  const [hasText, setHasText] = useState(false);
  
  return (
    <div class='parent'>
        <Child hasText={hasText} setHasText={setHasText} />
    </div>
  );
};

const Child = (props) => {
    const {hasText, setHasText} = props;
    function checkText() {
        ...
        const text = ...;
        if (text === '')    setHasText(false);
        else setHasText(true);
    }
    return (
        <div class='parent'>
            <button onKeyUp={() => {
                checkText()}
            }>
                send data to parent
            </button>
            {!hasText && <div className='placeholder'>댓글을 입력해주세요.</div>}
        </div>
    );
};

```

