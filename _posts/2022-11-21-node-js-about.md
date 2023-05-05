---
title: 3장 노드 기능 알아보기
categories:
- Node JS
feature_image: "https://w7.pngwing.com/pngs/445/982/png-transparent-node-js-express-js-javascript-mongodb-npm-logo-open-leaf-text-logo.png"
---

이번 챕터에서는 노드에서 사용하는 모듈인 CommonJS와 ECMAScript 모듈(ES 모듈)에 대해서 알아보자.

##### 3.1 REPL 사용하기

- 자바스크립트는 스크립트 언어이므로 미리 컴파일하지 않아도 즉석에서 코드를 실행할 수 있다. 입력한 코드를 읽고(Read), 해석하고(Eval), 결과물을 반환하고(Print), 종료할 때까지 반복한다(Loop)고 해서 REPL(Reacl Eval Print Loop)이라고 한다.
- 노드의 REPL을 사용하려면 윈도에서는 명령 프롬프트, 맥이나 리눅스에서는 터미널을 열고 node를 입력한다

##### 3.2 JS 파일 사용하기

- REPL에 직접 코드를 입력하는 대신, 자바스크립트 파일을 만들어 실행해 보자.

```js
function helloWorldO {
    console.log('Hello World')；
    helloNode()；
}
function helloNode() {
    console.log('Hello Node')；
}

helloWorld()；
```

- 콘솔에서 node ［자바스크립트 파일 경로］로 실행한다. (확장자(.js)는 생략 가능)

##### 3.3 JS 파일 사용하기

- 노드는 코드를 모듈로 만들 수 있다는 점에서 브라우저의 자바스크립트와는 다르다. **모듈이란 특정한 기능을 하는 함수나 변수들의 집합을 말한다.** 예를 들면 수학에 관련된 코드들만 모아서 모듈을 하나 만들 수 있다. 모듈은 자체로도 하나의 프로그램이면서 다른 프로그램의 부품으로도 사용할 수 있다. 
- **모듈로 만들어두면 여러 프로그램에 해당 모듈을 재사용할 수 있다.** 자바스크립트에서 코드를 재사용하기 위해 함수로 만드는 것과 비슷하다.
- 보통 파일 하나가 모듈 하나가 되며, 파일별로 코드를 모듈화할 수 있어 관리하기 편하다.
- 노드에서는 두 가지 형식의 모듈을 사용하는데, 하나는 CommonJS 모듈이고 다른 하나는 ECMAScript 모듈이다.

##### 3.3.1 CommonJS 모듈

- CommonJS 모듈은 표준 자바스크립트 모듈은 아니지만 노드 생태계에서 가장 널리 쓰이는 모듈이다. 왜 표준이 아닌데도 널리 쓰일까? 표준이 나오기 이전부터 쓰였기 때문이다.
- var.js와 func.js, index.js를 같은 폴더에 만들어보자.

var.js

```js
const odd = 'CJS 홀수입니다';
const even = 'CJS 짝수입니다';
module.exports = {
    odd,
    even,
}
```

- module.exports에 변수들을 담은 객체를 대입했다. 이제 이 파일은 모듈로서 기능한다.
- 이번에는 var.js를 참조하는 func.js를 작성해보자.

func.js

```js
const { odd, even } = require('./var')；
function checkOddOrEven(num) {
    if (num % 2) { // 홀수이면
        return odd；
    }
        return even；
}
module.exports = checkOddOrEven；
```

- 위 예제에서는 같은 폴더 안에 파일을 만들었지만, 다른 폴더에 있는 파일도 모듈로 사용할 수 있다. require 함수의 인수로 제공하는 경로만 잘 지정하면 된다.
- var.js의 module.exports에 담겨 있던 객체를 불러와 func.js에서 사용하는 모습이다. 물론 const obj = require('./var');로 객체를 통째로 불러온 뒤 obj.odd, obj.even처럼 접근할 수도 있다.
- 모듈 하나가 여러 개의 모듈을 사용할 수 있다. 그리고 모듈로부터 값을 불러올 때 변수 이름을 다르게 지정할 수도 있다.
- 여러 파일에 걸쳐 재사용되는 함수나 변수를 모듈로 만들어두면 편리하다. 그러나 모듈이 많아지고 모듈 간의 관계가 얽히게 되면 구조를 파악하기 어렵다는 단점도 있다.

##### 노드에서 this는 무엇일까?

- 노드에서의 this는 브라우저의 this와 조금 다르다.

```js
console.log(this)；
console.log(this === module.exports)；
console.log(this === exports)；
function whatIsThis() {
    console.log('function', this = exports, this === global)；
}
whatlsThis()；
```

```
{}
true
true
function false true
```

- 다른 부분은 브라우저의 자바스크립트와 동일하지만 최상위 스코프에 존재하는 this는 module.exports（또는 exports 객체）를 가리킨다. 또한, 함수 선언문 내부의 this는 3.4.1 절에서 배울 global 객체를 가리킨다.

- 이번에는 모듈을 불러오는 require에 대해 알아보자. require는 함수이고, 함수는 객체이므로 require는 객체로서 속성을 몇 개 갖고 있다. 그중에서 require.cache와 require.main을 알아보자.

require.js

```js
console.log（'require가 가장 위에 오지 않아도 됩니다.'）;
module.exports = '저를 찾아보세요.';
require('./var')；
console.log('require.cache입니다.');
console.log(require.cache)；
console.log('require.main입니다.')；
console.log(require.main === module)；
console.log(require.main.filename)；
```

```
require가 가장 위에 오지 않아도 됩니다.
require, cache입니다.
[Object： null prototype] {
        'C:\\Users\\zerocho\\require. js' ： Module {
        id：'.'
        exports：'저를 찾아보세요.',
        filename： 'C://Users//zerocho//require.js',
        loaded： false,
        children： [ [Module] ]
        paths： [
        'C:\\Users\\zerocho\\node_modules',
        'C:\\Users\\node_modules',
        'C:\\node_modules'
        ]
    },
'C\\Users\\\zerocho\\vars.js': Module {
        id：'C\\Users\\\zerocho\\vars.js',
        exports： { odd 'CJS 홀수 입니다.'： even：'CJS 짝수입니다.'},
        filename： ： 'C\\Users\\\zerocho\\vars.js',
        loaded： true,
        children： [],
        paths： [
        'C：\\Users\\zerocho\\node_modules'z
        'C:\\Users\\nodejnodules'z
        'C：\\node_modules’
        ]
    }
}
require.main입니다.
true
C:\\Users\\zerocho\\require. js
```

- 위 예제에서 알아야 할 점은 require가 반드시 파일 최상단에 위치할 필요가 없고, module.exports도 최하단에 위치할 필요가 없다는 것이다. 아무 곳에서나 사용해도 된다.
- require.cache 객체에 require.js나 var.js 같은 파일 이름이 속성명으로 들어 있는 것을 볼 수 있다. 속성값으로는 각 파일의 모듈 객체가 들어 있다. **한번 require한 파일은 require.cache에 저장**되므로 다음 번에 require할 때는 새로 불러오지 않고 require.cache에 있는 것이 재사용된다.
- require.main은 노드 실행 시 첫 모듈을 가리킨다. 현재 node require로 실행했으므로 require.js가 require.main이 된다.
- 현재 파일이 첫 모듈인지 알아보려면 require.main === module을 해보면 된다.
- 모듈을 사용할 때는 주의해야 할 점이 있다. 만약 두 모듈 dep1과 dep2가 있고 이 둘이 서로를 require한다면 어떻게 될까?

dep1.js

```js
const dep2 = require('./dep2')；
console.log('require dep2', dep2)； 
module.exports =()=>{
    console.log('dep2', dep2)；
}；
```
dep2.js

```js
const dep1 = require('./dep1')；
console.log('require dep1', dep1)； 
module.exports =()=>{
    console.log('dep1', dep1)；
}；

```
dep-run.js

```js
const dep1 = require('./dep1')；
const dep2 = require('./dep2')；

dep1()；
dep2()；
```

- 코드가 위에서부터 실행되므로 require('./dep1')이 먼저 실행된다. dep1.js에서는 제일 먼저 require('./dep2')가 실행된다. 다시 dep2.js에서는 require('./dep1')이 실행된다. 이과정이 계속 반복될것 같은데..?
- 놀랍게도 dep1의 module.exports가 함수가 아니라 빈 객체로 표시된다. 이러한 현상을 순환참조라고 부른다. 이렇게 순환 참조가 있을 경우에는 순환 참조되는 대상을 빈 객체로 만든다.

##### 3.3.2 ECMAScript 모듈

- ECMAScript 모듈（이하 ES 모듈）은 공식적인 자바스크립트 모듈 형식이다. 노드에서 아직까지는 CommonJS 모듈을 많이 쓰긴 하지만, ES 모듈이 표준으로 정해지면서 점점 ES 모듈을 사용하는 비율이 늘어나고 있다. 브라우저에서도 ES 모듈을 사용할 수 있어 브라우저와 노드 모두에 같은 모듈 형식을 사용할 수 있다는 것이 장점이다.
- 이전 절의 코드를 ES 모듈 스타일로 바꿔보자.

var.mjs

```js
export const odd = 'MJS 홀수입니다';
export const even = 'MJS 짝수입니다';
```

func.mjs

```js
import { oddz even } from './var.mjs'；
function checkOddOrEven(num) {
    if (num % 2) { // 홀수이면
    return odd；
    }
    return even；
}

export default checkOddOrEven；
```

index.mjs

```js
import { odd, even } from './var.mjs'；
import checkNumber from './func.mjs'；
function checkStringOddOrEven(str) {
    if (str.length % 2) {
        return odd；
    }
        return even；
}
console.log(checkNumber(10));
console.log(checkStringOddOrEven('hello'))；
```

- require와 exports, module.exports가 각각 import, export, export default로 바뀌었다. **ES 모듈의 import나 export default는 require나 module처럼 함수나 객체가 아니라 문법 그 자체이다.**
- js 확장자에서 import를 사용하면 SyntaxError： Cannot use import statement outside a module 에러가 발생한다. mjs 확장자 대신 js 확장자를 사용하면서 ES 모듈을 사용하려면 5장에서 볼 package.json에 type： Hmodule” 속성을 넣으면 된다.

##### 3.3.3 다이내믹 임포트

- CommonJS 모듈과 ES 모듈을 비교할 때, CommonJS 모듈에서는 다이내믹 임포트(dynamic import, 동적 불러오기)가 되는데 ES 모듈에서는 다이내믹 임포트가 안 된다고 설명했다. 다이내믹 임포트가 무엇이고 ES 모듈에서는 어떤 다른 방식을 사용하는지 알아보자.

dynamic.js

```js
const a = false；
if (a) {
    require('./func')；
}
console.log('성공')；
```

- dynamic.js에서 require('./func1)는 실행되지 않는다. if문이 false라서 실행되지 않으니까. 이렇게 **조건부로 모듈을 불러오는 것을 다이내믹 임포트**라고 합니다.

dynamic.mjs

```js
const a = false；
if (a) {
    import './func.mjs'；
}
console.log('성공')；
```

- 하지만 ES 모듈은 if문 안에서 import하는 것이 불가능하다. 이럴 때 다이내믹 임포트를 사용한다. dynamic.mjs를 다음과 같이 수정해 보자.

dynamic.mjs

```js
const a = true；
if (a) {
    const m1 = await import('./func.mjs')；
    console.log(m1)；
    const m2 = await import('./var.mjs')；
    console.log(m2)；
}
```

- import라는 함수를 사용해서 모듈을 동적으로 불러올 수 있다. import는 Promise를 반환하기에 await이나 then을 붙여야 한다.

##### 3.3.4 __filename, __dirname

- 노드에서는 파일 사이에 모듈 관계가 있는 경우가 많으므로 현재 파일의 경로나 파일명을 알아야하는 경우가 있다. 노드는 __filename, __dirname이라는 키워드로 경로에 대한 정보를 제공한다. **파일에 __filename과 __dirname을 넣어두면 실행 시 현재 파일명과 현재 파일 경로로 바뀐다.**

filename.js

```js
console.log(__filename)；
console.log(__dirname)；
```

```
C:\Users\zerocho\filename.js
C:\Users\zerocho
```

- 윈도가 아니라면 \ 대신 /로 폴더 경로가 구분될 수 있다. 이렇게 얻은 정보를 사용해서 경로 처리를 할 수도 있다. 하지만 경로가 문자열로 반환되기도 하고, \나 / 같은 경로 구분자 문제도 있으므로 보통은 이를 해결해주는 path 모듈과 함께 쓴다.
- 참고로 ES 모듈에서는 __filename과 __dirname을 사용할 수 없다. 대신 import.meta.url로경로를 가져올 수 있다.

<h3>끝!</h3>