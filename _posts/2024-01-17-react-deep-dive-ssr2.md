---
title: 4장 서버 사이드 렌더링(2)
categories:
- React Deep Dive
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

#### 4.2 서버 사이드 렌더링을 위한 리액트 API 살펴보기

기본적으로 리액트는 프런트엔드 라이브러리로 브라우저 자바스크립트 환경에서 렌더링할 수 있는 방법을 제공하지만 이와 동시에 리액트 애플리케이션을 서버에서 렌더링할 수 있는 API도 제공한다. **이 API는 당연히 브라우저의 window 환경이 아닌 Node.js와 같은 서버 환경에서만 실행할 수 있으며 window 환경에서 실행 시 에러가 발생할 수 있다.**

##### 4.2.1 renderToString

- renderToString은 함수 이름에서도 알 수 있듯이 인수로 넘겨받은 리액트 컴포넌트를 렌더링해 HTML 문자열로 반환하는 함수다. 서버 사이드 렌더링을 구현하는 데 가장 기초적인 API로, 최초의 페이지를 HTML로
먼저 렌더링한다고 언급했는데 바로 그 역할을 하는 함수가 renderToString이다.

```js
function ChildrenComponent({ fruits }: { fruits: Array<string> }) {
    useEffect(() => {
        console.log(fruits)
    }, [fruits])

    function handleClick() {
        console. log('hello')
    }
    return (
        <ul>
        {fruits.map((fruit) => (
            <li key={fruit} onClick={handleClick}>
        {fruit}
        </li>
        ))}
        </ul>
    )
}
function SampleComponentO {
    return (
        <>
        <div>hello</div>
        <ChildrenComponent fruits={['apple', 'banana', 'peach']} />
        </>
    )
}
const result = ReactDOMServer.renderToString(
    React.createElement('div', { id: 'root' }, 〈SampleComponent />),
)
```

- **여기서 한 가지 눈여겨볼 것은 ChildrenComponent에 있는 useEffect와 같은 훅과 handleClick과 같은 이벤트 핸들러는 결과물에 포함되지 않았다는 것이다.** 이것은 의도된 것으로, renderToString은 인수로 주어진 리액트 컴포넌트를 기준으로 빠르게 브라우저가 렌더링할수 있는 HTML을 제공하는 데 목적이 있는 함수일 뿐이다. 필요한 자바스크립트 코드는 여기에서 생성된 HTML과는 별도로 제공해 브라우저에 제공돼야 한다.
- renderToString을 사용하면 앞서 언급했던 서버 사이드의 이점, 클라이언트에서 실행되지 않고 일단 먼저 완성된 HTML을 서버에서 제공할 수 있으므로 초기 렌더링에서 뛰어난 성능을 보일 것이다.
- 여기서 한 가지 중요한 사실은 **리액트의 서버 사이드 렌더링은 단순히 ‘최초 HTML 페이지를 빠르게 그려주는 데’에 목적이 있다는 것**이다. 사용자는 완성된 HTML을 빠르게 볼 수는 있지만 **useEffect나 이벤트 핸들러가 없는 것을 확인할 수 있듯이 실제로 웹페이지가 사용자와 인터랙션할 준비가 되기 위해서는 이와 관련된 별도의 자바스크립트 코드를 모두 다운로드, 파싱, 실행하는 과정을 거쳐야 한다.**


<h5>싱글 펭지 애플리케이션이란?</h5>


<h3>끝!</h3>
