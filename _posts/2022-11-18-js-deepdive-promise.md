---
title: 45장 프로미스
categories:
- Modern JavaScript DeepDive
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

- 자바스크립트는 비동기 처리를 위한 하나의 패턴으로 콜백 함수를 사용한다. 하지만 전통적인 콜백 패턴은 콜백 헬로 인해 가독성이 나쁘고 비동기 처리 중 발생한 에러의 처리가 곤란하며 여러 개의 비동기 처리를 한 번에 처리하는 데도 한계가 있다.
- **ES6에서는 비동기 처리를 위한 또 다른 패턴으로 프로미스를 도입**했다. 프로미스는 전통적인 콜백 패턴이 가진 단점을 보완하며 비동기 처리 시점을 명확하게 표현할 수 있다는 장점이 있다.

#### 45.1 비동기 처리를 위한 콜백 패턴의 단점

##### 45.1.1 콜백 헬

- 먼저 44.3.4절 “GET 요청”에서 살펴본 바와 같이 GET 요청을 위한 함수를 작성해 보자.

```js
// GET 요청을 위한 비동기 함수
const get = url => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.send();
    xhr.onload =()=>{
        if (xhr.status === 200) {
            // 서버의 응답을 콘솔에 출력핸다.
            console.log(JSON.parse(xhr.response));
        } else {
            console.error('${xhr.status} ${xhr.statusText}')；
        }
    }； 
}；
// id가 1인 post를 취득
get('https://jsonplaceholder.typicode.eom/posts/1');
/*
{
"userid": 1,
"id": 1,
"title": "sunt aut facere ... ",
"body": "quia et suscipit "
}
*/
```

- 



<h3>끝!</h3>
