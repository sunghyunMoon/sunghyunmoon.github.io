---
title: 3.4장 노드 내장 객체 알아보기
categories:
- Node JS
feature_image: "https://w7.pngwing.com/pngs/445/982/png-transparent-node-js-express-js-javascript-mongodb-npm-logo-open-leaf-text-logo.png"
---

내장 객체와 내장 모듈은 따로 설치하지 않아도 바로 사용할 수 있으며, 브라우저의 window 객체와 비슷하다고 보면 된다.
이 절에서는 노드 프로그래밍을 할 때 많이 쓰이는 내장 객체를 알아보자.

##### 3.4.1 global

- 브라우저의 window와 같은 전역 객체이며, 전역 객체이므로 모든 파일에서 접근할 수 있다. 또한, window.open 메서드를 그냥 open으로 호출할 수 있는 것처럼 global도 생략할 수 있다.
- 내용이 너무 많아 줄였지만 global 객체 안에는 수십 가지의 속성이 담겨 있다. 그 속성 모두를 알 필요는 없고, 자주 사용하는 속성들만 이 절에서 알아보자.
- 전역 객체라는 점을 이용해 파일 간에 간단한 데이터를 공유할 때 사용하기도 한다. globalA.js와 globalB.js를 같은 폴더에 생성해보자.

```globalA.js
module.exports = () => global.message；
```

```globalB.js
const A = require('./globalA')；

global.message = '안녕하세요';
console.log(A())；
```

console output

```
안녕하세요
```

- 콘솔 결과를 보면, globalB에서 넣은 global.message 값을 globalA에서도 접근할 수 있음을 알 수 있다.

##### 3.4.2 console

- 브라우저에서의 console과 거의 비슷하다. 
    - console.time（레이블）: console.timeEnd（레이블）과 대응되어 같은 레이블을 가진 time과 timeEnd 사이의 시간을 측정한다.
    - console.error（에러 내용）: 에러를 콘솔에 표시한다.
    - console.table（배열）: 배열의 요소로 객체 리터럴을 넣으면, 객체의 속성들이 테이블 형식으로 표현된다.
    - console.dir（객체, 옵션）: 객체를 콘솔에 표시할 때 사용한다. 첫 번째 인수로 표시할 객체를 넣고, 두 번째 인수로 옵션을 넣는다. 옵션의 colors를 true로 하면 콘솔에 색이 추가되어 보기가 한결 편해진다. depth는 객체 안의 객체를 몇 단계까지 보여줄지를 결정한다. 기본값은 2다.
    - console.trace（레이블）: 에러가 어디서 발생했는지 추적할 수 있게 한다. 보통은 에러 발생 시 에러 위치를 알려주므로 자주 사용하지 않지만, 위치가 나오지 않는다면 사용할 만하다.

##### 3.4.3 타이머

- 타이머 기능을 제공하는 함수인 setTimeout, setinterval, setlmediate는 노드에서 window 대신 global 객체 안에 들어 있다.
    - setTimeout（콜백 함수, 밀리초）: 주어진 밀리초（1.000분의 1초） 이후에 콜백 함수를 실행한다.
    - setinterval（콜백 함수, 밀 리초）: 주어진 밀리초마다 콜백 함수를 반복 실행한다.
    - set1mmediate（콜백 함수）: 콜백 함수를 즉시 실행한다.
- 이 타이머 함수들은 모두 아이디를 반환한다. 아이디를 사용하면 타이머를 취소할 수 있다.
    - clearTimeout（아이디）: setTimeout을 취소한다.
    - clearinterval（아이디）: setinterval을 취소한다.
    - clearlmmediate（아이디）: setImmediate를 취소한다.
- setlmmediate（콜백）과 setTimeout(콜백, 0)
    - setlmmediate（콜백）과 setTimeout（콜백, 0）에 담긴 콜백 함수는 이벤트 루프를 거친 뒤 즉시 실행된다. 특수한 경우에 setlmmediate는 setTimeout（콜백, 0）보다 먼저 실행된다. 파일 시스템 접근, 네트워킹 같은 I/O 작업의 콜백 함수 안에서 타이머를 호출하는 경우다. 하지만 setlmmediate가 항상 setTimeout（콜백, 0）보다 먼저 호출되는 것은 아니라는 사실만 알아두자. 헷갈리지 않도록 setTimeout（콜백, 0）은 사용하지 않는 것을 권장한다.

##### 3.4.4 process

- process 객체는 현재 실행되고 있는 노드 프로세스에 대한 정보를 담고 있습니다. process 객체 안에는 다양한 속성이 있다.

###### 3.4.4.1 process.env

-  process.env를 입력하면 매우 많은 정보가 출력된다. 자세히 보면 이 정보들이 시스템의 환경 변수임을 알 수 있다. 시스템 환경 변수가 노드에 직접 영향을 미치기도 한다. 대표적인 것으로 UV_THREADPOOL_SIZE와 NODE_OPTIONS가 있다.

```
N0DE_0PTI0NS=--max-old-space-size=8192
UV_THREADP00L_SIZE=8
```

- 왼쪽이 환경 변수의 이름이고 오른쪽이 값이다. NODE_OPTIONS는 노드를 실행할 때의 옵션들을 입력받는 환경 변수이다. --max-old-space-size=8192는 노드의 메모리를 8GB까지 사용할 수 있게 한다.
- UV_THREADPOOL_SIZE는 노드에서 기본적으로 사용하는 스레드 풀의 스레드 개수를 조절할 수 있게한다.
- 시스템 환경 변수 외에도 우리가 임의로 환경 변수를 저장할 수 있다. process.env는 서비스의 중요한 키를 저장하는 공간으로도 사용된다. 서버나 데이터베이스의 비밀번호와 각종 API 키를 코드에 직접 입력하는 것은 위험하다. 혹여 서비스가 해킹당해 코드가 유출될 경우, 비밀번호가 코드에 남아 있어 추가 피해가 발생할 수 있다.
- 따라서 중요한 비밀번호는 다음과 같이 process.env의 속성으로 대체한다.

```
const secretld = process.env.SECRET_ID；
const secretCode = process.env.SECRET_CODE；
```

###### 3.4.4.2 process.nextTick(콜백)

- 이벤트 루프가 다른 콜백 함수들보다 nextTick의 콜백 함수를 우선으로 처리하도록 만든다.

```js
setlmmediate(() => {
    console.log('immediate')；
});
process.nextTick(() => {
    console.log('nextTick')；
})；
setTimeout(() => {
    console.log('timeout')；
}, 0)； 
Promise.resolve().then(() => console.log('promise1))；
```

콘솔

```
nextTick
promise
timeout
immediate
```

- **process.nextTick은 setlmmediate나 setTimeout보다 먼저 실행된다.** 코드 맨 밑에 Promise를 넣은 것은 resolve된 Promise도 nextTick처럼 다른 콜백들보다 우선시되기 때문이다. 그래서 **process.nextTick과 Promise를 마이크로태스크(microtask)라고 따로 구분해서 부른다.**

- 마이크로태스크의 재귀 호출
    - process.nextTick으로 받은 콜백 함수나 resolve된 Promise는 다른 이벤트 루프에서 대기하는 콜백 함수보다도 먼저 실행된다. 그래서 비동기 처리를 할 때 setlmmediate보다 process.nextTick을 더 선호하는 개발자도있다. 하지만 이런 마이크로태스크를 재귀 호출하게 되면 이벤트 루프는 다른 콜백 함수보다 마이크로태스크를 우선해 처리하므로 콜백 함수들이 실행되지 않을 수도 있다.

###### 3.4.4.3 process.exit(코드)

- 실행 중인 노드 프로세스를 종료한다. 서버 환경에서 이 함수를 사용하면 서버가 멈추므로 특수한 경우를 제외하고는 서버에서 잘 사용하지 않는다. 하지만 서버 외의 독립적인 프로그램에서는 수동으로 노드를 멈추기 위해 사용한다.
- process.exit 메서드는 인수로 코드 번호를 줄 수 있다. 인수를 주지 않거나 0을 주면 정상종료를 뜻하고, 1을 주면 비정상 종료를 뜻한다. 만약 에러가 발생해서 종료하는 경우에는 1을넣으면 된다.

<h3>끝!</h3>