---
title: 26장 ES6 함수의 추가 기능
categories:
- Modern JavaScript DeepDive
feature_image: "/assets/img/background/javascript.PNG"
---

ES6에서의 함수의 추가 기능에 대해서 알아보자.

#### 26.1 함수의 구분

- ES6 이전까지 자바스크립트의 함수는 별다른 구분 없이 다양한 목적으로 사용되었다. 자바스크립트의 함수
는 <span style="color:tomato; background-color:#fff5b1" >일반적인 함수로서 호출</span>할 수도 있고, new 연산자와 함께 호출하여  <span style="color:tomato; background-color:#fff5b1" >인스턴스를 생성할 수 있는 생성자 함수로서 호출</span>할 수도 있으며, <span style="color:tomato; background-color:#fff5b1" >객체에 바인딩되어 메서드로서 호출</span>할 수도 있다.
- 이는 언뜻 보면 편리한 것 같지만 실수를 유발시킬 수 있으며 성능 면에서도 손해다.
- 즉, <span style="color:tomato; background-color:#fff5b1" >ES6 이전의 모든 함수는 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다.</span> 다시 말해, ES6 이전의 모든 함수는 callable이면서 constructor다.
- 주의할 것은 ES6 이전에 일반적으로 메서드라고 부르던 객체에 바인딩된 함수도 callable이며 constructor 라는 것이다. 따라서 객체에 바인딩된 함수도 **일반 함수**로서 호출할 수 있는 것은 물론 **생성자 함수**로서 호출할 수도 있다.


``` js
// 프로퍼티 f에 바인딩된 함수는 callable이며 constructor다.
var obj = {
    x: 10,
    f: function () { return this.x; }
}；
// 프로퍼티 f에 바인딩된 함수를 메서드로서 호출
console.log(obj.f()); // 10
// 프로퍼티 f에 바인딩된 함수를 일반 함수로서 호출
var bar = obj.f;
console.log(bar()); // undefined
// 프로퍼티 f에 바인딩된 함수를 생성자 함수로서 호출
console.log(new obj.f()); // f {}
```
- 위 예제와 같이 객체에 바인딩된 함수를 생성자 함수로 호출하는 경우가 흔치는 않겠지만 문법상 가능하다는 것은 문제가 있다. 그리고 이는 성능 면에서도 문제가 있다. 객체에 바인딩된 함수가 constructor라는 것은 객체에 바인딩된 함수가 prototype 프로퍼티를 가지며. 프로토타입 객체도 생성한다는 것을 의미하기 때문 이다. 
- 정리를 하면, 이처럼 <span style="color:tomato; background-color:#fff5b1">ES6 이전의 모든 함수는 사용 목적에 따라 명확한 구분이 없으므로 호출 방식에 특별한 제약이 없고 생성자 함수로 호출되지 않아도 프로토타입 객체를 생성한다. 이는 혼란스러우며 실수를 유발할 가능성이 있고 성능에도 좋지 않다.</span>
-  ES6에서는 함수를 사용 목적에 따라 세 가지 종류로 명확히 구분했다.
<div><img src= "/assets/img/post/es6_function.PNG"></div>

#### 26.2 메서드

- ES6 이전 사양에는 메서드에 대한 명확한 정의가 없었다. 일반적으로 메서드는 객체에 바인딩된 함수를 일컫는 의미로 사용되었다. ES6 사양에서는 메서드에 대한 정의가 명확하게 규정되었다. <span style="color:tomato; background-color:#fff5b1">ES6 사양에서 메서드는 메서드 축약 표현으로 정의된 함수만을 의미한다.</span>
- <span style="color:tomato; background-color:#fff5b1">ES6 사양에서 정의한 메서드（이하 ES6 메서드）는 인스턴스를 생성할 수 없는 non-constructor다.</span> 따라서 ES6 메서드는 생성자 함수로서 호출할 수 없다.
- <span style="color:tomato; background-color:#fff5b1">ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 [[HomeObject]]를 갖는다.</span>  super 참조는 내부 슬롯 [[HomeObject]]를 사용하여 수퍼클래스의 메서드를 참조하므로 내부 슬롯 [[HomeObject]]를 갖는 ES6 메서드는 super 키워드를 사용할 수 있다.

``` js
const derived = {
    __proto__: base,
    // sayHi는 ES6 메서드가 아니다.
    // 따라서 sayHi는 [[HomeObject]]를 갖지 않0므루 super 키워드를 사용할 수 없다.
    sayHi: function () {
        // SyntaxError: 'super' keyword unexpected here
        return ${super .sayHi()} . : How are you doing?' ;
    }
};

```
- 이처럼 ES6 메서드는 본연의 기능(super)을 추가하고 의미적으로 맞지 않는 기능(constructor)은 제거했다. 따라서 메서드를 정의할 때 프로퍼티 값으로 익명 함수 표현식을 할당하는 ES6 이전의 방식은 사용하지 않는 것이 좋다.

#### 26.3 화살표 함수

- 화살표 함수arrow function는 function 키워드 대신 화살표(=>)를 사용하여 기존의 함수 정의 방식보다 간략하게 함수를 정의할 수 있다. 화살표 함수는 표현만 간략한 것이 아니 라 내부 동작도 기존의 함수보다 간략하다. 

##### 26.3.1 화살표 함수 정의

- 화살표 함수는 함수 선언문으로 정의할 수 없고 함수 표현식으로 정의해야 한다.
- 매개변수가 여러 개인 경우 소괄호 () 안에 매개변수를 선언한다.
- 매개변수가 한 개인 경우 소괄호 ()를 생략할 수 있다.
- 매개변수가 없는 경우 소괄호 ()를 생략할수 없다.
- 함수 몸체가 하나의 문으로 구성된다면 함수 몸체를 감싸는 중괄호 {}를 생략할 수 있다. 이때 함수 몸체 내부의 문이 값으로 평가될 수 있는 표현식인 문이라면 암묵적으로 반환된다.

``` js
// concise body
const power = x => x ** 2;
power(2); // — 4
// 위 표현은 다음과 동일하다.
// block body
const power = x => { return x ** 2; };
```

- 객체 리터 럴을 반환하는 경우 객체 리터럴을 소괄호 ()로 감싸 주어야 한다.

``` js
const create = (id, content) => ({ id, content });
create(l, ' JavaScript'); // {id: 1, content: "JavaScript"}
// 위 표현은 다음과 동일하다.
const create = (id, content) => { return { id, content }; };
```

- 화살표 함수도 즉시 실행 함수로 사용할 수 있다.
- 화살표 함수도 일급 객체이므로 Array.prototype.map, Array.prototype.filter, Array.prototype.reduce 같은 고차 함수에 인수로 전달할 수 있다. 이 경우 일반적인 함수 표현식보다 표현이 간결하고 가독성이 좋다.
- 일반 함수와 화살표 함수의 차이에 대해 살펴보자.

##### 26.3.2 화살표 함수와 일반 함수의 차이
1. 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor다.
2. 중복된 매개변수 이름을 선언할 수 없다.
3. 화살표 함수는 함수 자체의 this, arguments, super, new. target 바인딩을 갖지 않는다.
- 따라서 화살표 함수 내부에서 this, arguments, super, new.target을 참조하면 스코프 체인의 상위 스코프의 this, arguments, super, new.target을 참조한다.

##### 26.3.3 this

- 화살표 함수의 this는 일반 함수의 this와 다르게 동작한다. 이는 “콜백 함수 내부의 this 문제”, 즉 콜백 함 수 내부의 this가 외부 함수의 this와 다르기 때문에 발생하는 문제를 해결하기 위해 의도적으로 설계된 것 이다.

``` js
class Prefixer {
    constructor(prefix) {
        this.prefix = prefix;
    }
    add(arr) {
        // add 메서드는 인수로 전달된 배열 arr을 순회하며 배열의 모든 요소에 prefix를 추가한다.
        // 1
        return arr.map(function (item) {
        return this.prefix + item; // 2
        // TypeError: Cannot read property 'prefix' of undefined
        });
    }
}
const prefixer = new Prefixer('-webkit-1');
console.log(prefixer.add([' transition', 'user-select']));
```

- TypeError가 발생한다. 이유는?
- 프로토타입 메서드 내부인 1에서 this는 메서드를 호출한 객체(위 예제의 경우 prefixer 객체)를 가리킨다.
- 그런데 Array. prototype, map의 인수로 전달한 콜백 함수의 내부인 2에서 this는 undefined를 가리킨다. 이는 Array.prototype.map 메서드가  <span style="color:tomato; background-color:#fff5b1">콜백 함수를 일반 함수</span>로서 호출하기 때문이다.
- 22장 “this”에서 살펴보았듯이 일반 함수로서 호출되는 모든 함수 내부의 this는 전역 객체를 가리킨다. strict mode에서 일반 함수로서 호출된 모든 함수 내부의 this에는 전역 객체가 아니라 undefined가 바인딩되므로 일반 함수로서 호출되는 Array.prototype.map 메서드의 콜백 함수 내부의 this에는 undefined가 바인딩된다.
-  <span style="color:tomato; background-color:#fff5b1">즉, 콜백 함수의 this(2)와 외부 함수의 this(1)가 서로 다른 값을 가리키고 있기 때문에 TypeError가 발생한 것이다.</span>
- ES6에서는 화살표 함수를 사용하여 “콜백 함수 내부의 this 문제”를 해결할 수 있다.

``` js
class Prefixer {
    constructor(prefix) {
     this.prefix = prefix;
    }
    add(arr) {
        return arr.map(item => this.prefix + item);
    }
}
const prefixer = new Prefixer('-webkit-');
console.log(prefixer.add(['transition', 'user-select']))；
// ['-webkit-transition’, '-webkit-user-select']

```

- <span style="color:tomato; background-color:#fff5b1">화살표 함수는 함수 자체의 this 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 this를 참조하면 상위
스코프의 this를 그대로 참조한다. 이를 lexical this라 한다.</span>
- 반대로, 화살표 함수를 제외한 모든 함수에는 this 바인딩이 반드시 존재한다. <span style="color:tomato; background-color:#fff5b1">화살표 함수 내부에서 this를 참조하면 일반적인 식별자처럼 스코프 체인을 통해 상위 스코프에서 this를 탐색한다.</span>
- 프로퍼티에 할당한 화살표 함수도 스코프 체인 상에서 가장 가까운 상위 함수 중에서 화살표 함수가 아닌 함수의 this를 참조한다.

``` js
// increase 프로퍼티에 할당한 화살표 함수의 상위 스코프는 전역이다.
// 따라서 increase 프로퍼티에 할당한 화살표 함수의 this는 전역 객체를 가리킨다.
const counter = {
    num: 1,
    increase: () => ++this.num
}；
console.log(counter.increase()); // NaN
```

- 화살표 함수는 함수 자체의 this 바인딩을 갖지 않기 때문에 Function.prototype.call, Function.prototype.apply, Function.prototype.bind 메서드를 사용해도 화살표 함수 내부의 this를 교체할 수 없다.
- 메서드를 화살표 함수로 정의하는 것은 피해야 한다.

``` js
// Bad
const person = {
    name: 'Lee',
    sayHi: () => console.log('Hi ${this.name}')
};
// sayHi 프로퍼티에 할당된 화살표 함수 내부의 this는 상위 스코프인 전역의 this가 가리카는
// 전역 객체를 가리키므로 이 예제를 브라우저에서 실행하면 this.name은 빈 문자열을 갖는 window.name과 같다.
// 전역 객체 window에는 빌트인 프로퍼티 nameOl 존재한다
```

- 위 예제의 경우 sayHi 프로퍼티에 할당한 화살표 함수 내부의 this는 메서드를 호출한 객체인 person을 가리키지 않고 상위 스코프인 전역의 this가 가리키는 전역 객체를 가리킨다. 
- 따라서 화살표 함수로 메서드를 정의하는 것은 바람직하지 않다. <span style="color:tomato; background-color:#fff5b1">메서드를 정의할 때는 ES6 메서드 
``` js
// Good
const person = {
    name: 'Lee',
    sayHiO {
    console.log( Hi ${this.name} );
    }
}；
person.sayHi(); // Hi Lee
```

- 클래스 필드 정의 제안을 사용하여 클래스 필드에 화살표 함수를 할당할 수도 있다.

``` js
// Bad
class Person {
    // 클래스 필드 정의 제안
    name = 'Lee';
    sayHi = () => console.log('Hi ${this.name}');
    const person = new Person();
    person.sayHi(); // Hi Lee  
}
```

- 이때 sayHi 클래스 필드에 할당한 화살표 함수 내부에서 this를 참조하면 상위 스코프의 this 바인딩을 참조한다. <span style="color:tomato; background-color:#fff5b1">그렇다면 sayHi 클래스 필드에 할당한 화살표 함수의 상위 스코프는 무엇일까?</span>
- sayHi 클래스 필드는 인스턴스 프로퍼티이므로 다음과 같은 의미다.

``` js
class Person {
    constructor() {
    this.name = 'Lee';
    // 클래스가 생성한 인스턴스(this)의 sayHi 프로퍼티에 화살표 함수를 할당한다.
    // 따라서 sayHi 프로퍼티는 인스턴스 프로퍼티다.
    this.sayHi = () => console.log( Hi ${this.name}');
    }
}

```

- sayHi 클래스 필드에 할당한 화살표 함수의 상위 스코프는 사실 클래스 외부다. 하지만 this는 클래스 외부의 this를 참조하지 않고 클래스가 생성할 인스턴스를 참조한다. <span style="color:tomato; background-color:#fff5b1">따라서 sayHi 클래스 필드에 할당한 화살표함수 내부에서 참조한 this는 constructor 내부의 this 바인딩과 같다.</span> constructor 내부의 this 바인딩은클래스가 생성한 인스턴스를 가리키므로 sayHi 클래스 필드에 할당한 화살표 함수 내부의 this 또한 클래스가 생성한 인스턴스를 가리킨다.
- 하지만 클래스 필드에 할당한 화살표 함수는 프로토타입 메서드가 아니라 인스턴스 메서드가 된다. 따라서 메서드를 정의할 때는 ES6 메서드 축약 표현으로 정의한 ES6 메서드를 사용하는 것이 좋다.

##### 26.3.4 super

- 화살표 함수는 함수 자체의 super 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 super를 참조하면 this와 마찬가지로 상위 스코프의 super를 참조한다.
- this와 마찬가지로 클래스 필드에 할당한 화살표 함수 내부에서 super를 참조하면 constructor 내부의 super 바인딩을 참조한다.

##### 26.3.4 arguments

- 화살표 함수는 함수 자체의 arguments 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 arguments를 참조하면 this와 마찬가지로 상위 스코프의 arguments를 참조한다.

``` js
(function () {
    // 화살표 함수 foo의 arguments는 상위 스코프인 즉시 실행 함수의 arguments를 가리킨다.
    const foo = () => console.log(arguments); // [Arguments] { '0': 1, '1' ： 2 }
    foo(3, 4);
}(1, 2));
// 화살표 함수 foo의 arguments는 상위 스코프인 전역의 arguments를 가리킨다.
// 하지만 전역에는 arguments 객처/가 존재하지 않는다. arguments 객체는 함수 내부에서만 유효하다.
const foo = () => console.log(arguments);
foo(1, 2); // ReferenceError: arguments is not defined
```

#### 26.4 Rest 파라미터

##### 26.4.1 기본 문법

- Rest 파라미터(나머지 매개변수)는 매개변수 이름 앞에 세개의 점 … 을 붙여서 정의한 매개변수를 의미한다. Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받는다.
- 일반 매개변수와 Rest 파라미터는 함께 사용할 수 있다. 이때 함수에 전달된 인수들은 매개변수와 Rest 파라미터에 순차적으로 할당된다.
- Rest 파라미터는 이름 그대로 먼저 선언된 매개변수에 할당된 인수를 제외한 나머지 인수들로 구성된 배열이할당된다. 따라서 Rest 파라미터는 반드시 마지막 파라미터이어야 한다.
- Rest 파라미터는 단 하나만 선언할 수 있다.
- Rest 파라미터는 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 length 프로퍼티에 영향을 주지 않는다.

##### 26.4.2 Rest 파라미터와 arguments 객체

- arguments 객체는 함수 호출 시 전달된 인수들의 정보를 담고 있는 순회 가능한 유사 배열 객체이며. 함수 내부에서 지역 변수처럼 사용할수 있다.
- 하지만 arguments 객체는 배열이 아닌 유사 배열 객체이므로 배열 메서드를 사용하려면 Function.prototype.call이나 Function.prototype.apply 메서드를 사용해 arguments 객체를 배열로 변환해야 하는번거로움이 있었다.

``` js
function sum() {
    // 유사 배열 객체인 arguments 객체를 배열로 변환한다.
    var array = Array.prototype.slice.call(arguments);
    return array.reduce(function (pre, cur) {
        return pre + cur;
    }, 0)；
}
console.log(sum( 1, 2, 3, 4, 5)); // 15

```

- ES6에서는 rest 파라미터를 사용하여 가변 인자 함수의 인수 목록을 배열로 직접 전달받을 수 있다. 

``` js
function sum( ... args) {
    // Rest 파라미터 args에는 배열 [1, 2, 3, 4, 5J가 할당된다.
    return args.reduce((pre, cur) => pre + cur, 0);
}
console.log(sum(l, 2, 3, 4, 5)); // 15

```

#### 26.5 매개변수 기본값

- 함수를 호출할 때 매개변수의 개수만큼 인수를 전달하는 것이 바람직하지만 그렇지 않은 경우에도 에러가 발생하지 않는다. 이는 자바스크립트 엔진이 매개변수의 개수와 인수의 개수를 체크하지 않기 때문이다.
- 인수가 전달되지 않은 매개변수의 값은 undefined다. 이를 방치하면 의도치 않은 결과가나올 수 있다. 따라서 다음 예제와 같이 매개변수에 인수가 전달되었는지 확인하여 인수가 전달되지 않은 경우 매개변수에 기본값을 할당할 필요가 있다. 즉, 방어 코드가 필요하다.

``` js
function sum(x, y) {
    // 안수가 전달되지 않아 매개변수의 값이 undefined인 경우 기본값을 할당한다.
    x = x I! 0;
    y = y !! 0;
    return x + y;
}
console.log(sum(l, 2)); // 3
console.log(sum(l))； // 1

```

- ES6에서 도입된 매개변수 기본값을 사용하면 함수 내에서 수행하던 인수 체크 및 초기화를 간소화할 수 있다.

``` js
function sum(x = 0, y = 0) {
return x + y;
}
console.log(sum(l, 2)); // 3
console.log(sum(l))； // 1

```

- 매개변수 기본값은 매개변수에 인수를 전달하지 않은 경우와 undefined를 전달한 경우에만 유효하다.
- 매개변수 기본값은 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 length 프로퍼티와 arguments 객체에 아무런 영향을 주지 않는다.

<h3>끄~ㅌㅌㅌ!</h3>
