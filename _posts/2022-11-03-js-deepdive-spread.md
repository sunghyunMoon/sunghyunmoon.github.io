---
title: 35장 스프레드 문법
categories:
- Modern JavaScript DeepDive
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

- ES6에서 도입된 스프레드 문법spread syntax(전개 문법) ... 은 하나로 뭉쳐 있는 여러 값들의 집합을 펼쳐서(전개, 분산하여, spread) 개별적인 값들의 목록으로 만든다. 
- **스프레드 문법을 사용할 수 있는 대상**은 Array, String, Map. Set, DOM 컬렉션(NodeList, HTMLCollection),arguments와 같이 **for... of 문으로 순회할수 있는 이터러블에 한정**된다.

```js
// ... [1, 2, 3]은 [1, 2, 3]을 개별 요소로 분리한다(今 1, 2, 3).
console.log( ... [1, 2, 3]); // 1 2 3
// 문자열은 이터러블이다.
console.log( ... 'Hello'); //Hello
// Map과 Set은 이터러블이다.
console.log( ... new Map([[’a', '1'], ['b', '2']])); // [ 'a'1' ] [ 'b', '2' J
console.log( ... new Set([1, 2, 3])); // 1 2 3
// 이터러블이 아닌 일반 객체는 스프러/드 문법의 대상이 될 수 없다.
console.log( ... { a: 1, b: 2 });
// TypeError: Found non-callable @@iterator

```

- 위 예제에서 …[1, 2, 3]은 이터러블인 배열을 펼쳐서 요소들을 개별적인 값들의 목록 1 2 3으로 만든다. 이때 1 2 3은 값이 아니라 값들의 목록이다. 즉, **스프레드 문법의 결과는 값이 아니다.** 이는 스프레드 문법 ... 이 피연산자를 연산하여 값을 생성하는 연산자가 아님을 의미한다. **따라서 스프레드 문법의 결과는 변수에 할당할 수 없다.**

```js
// 스프레드 문법의 결과는 값이 아니다.
const list = ... [1, 2, 3]; // SyntaxError: Unexpected token ...
```

- 이처럼 스프레드 문법의 결과물은 값으로 사용할 수 없고, 다음과 같이 쉼표로 구분한 값의 목록을 사용하는 문맥에서만 사용할 수 있다.
    - 함수 호출문의 인수 목록
    - 배열 리터럴의 요소 목록
    - 객체 리터럴의 프로퍼티 목록

#### 35.1 함수 호출문의 인수 목록에서 사용하는 경우

- 요소들의 집합인 배열을 펼쳐서 개별적인 값들의 목록으로 만든 후, 이를 함수의 인수 목록으로 전달해야 하는 경우가 있다. 다음 예제를 살펴보자.

```js
const arr = [1, 2, 3];
// 배열 arr의 요소 중에서 최대값을 구하기 위해 Math.max를 사용한다.
const max = Math.max(arr); // — NaN
```

- 이 같은 문제를 해결하기 위해 배열을 펼쳐서 요소들을 개별적인 값들의 목록으로 만든 후, Math.max 메서드의 인수로 전달해야 한다. 즉, [1, 2, 3]을 1, 2, 3으로 펼쳐서 Math.max 메서드의 인수로 전달해야 한다.

```js
const arr = [1, 2, 3];
// 스프레드 문법을 사용하여 배열 arr을 1, 2, 3으로 펼쳐서 Math.max에 전달한다.
// Math.max(... [1, 2, 3J)은 Math.max(l, 2, 3)과 같다.
const max = Math.max(... arr); // — 3
```

- 스프레드 문법은 앞에서 살펴본 Rest 파라미터와 형태가 동일하여 혼동할 수 있으므로 주의할 필요가 있다.
- **Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받기 위해** 매개변수 이름 앞에 … 을 붙이는 것이다.
- **스프레드 문법은 여러 개의 값이 하나로 뭉쳐 있는 배열과 같은 이터러블을 펼쳐서 개별적인 값들의 목록을 만드는 것**이다.

#### 35.2 배열 리터럴 내부에서 사용하는 경우

- 스프레드 문법을 배열 리터럴에서 사용하면 ES5에서 사용하던 기존의 방식보다 더욱 간결하고 가독성 좋게 표현할 수 있다. ES5에서 사용하던 방식과 비교하여 살펴보도록 하자.

##### 35.2.1 concat

- ES5에서 2개의 배열을 1개의 배열로 결합하고 싶은 경우 배열 리터럴만으로 해결할 수 없고 concat 메서드를 사용해야 한다.

```js
// ES5
var arr = [1, 2]•concat([3, 4]);
console.log(arr); // [1, 2, 3, 4]
```

- 스프레드 문법을 사용하면 별도의 메서드를 사용하지 않고 배열 리터럴만으로 2개의 배열을 1개의 배열로 결합할 수 있다.

```js
// ES6
const arr = [...[1, 2], ... [3f 4]];
console.log(arr); // [1, 2, 3, 4]
```

##### 35.2.2 splice

- ES5에서 어떤 배열의 중간에 다른 배열의 요소들을 추가하거나 제거하려면 splice 메서드를 사용한다. 이때 splice 메서드의 세 번째 인수로 배열을 전달하면 배열 자체가 추가된다.

```js
// ES5
var arrl = [1, 4];
var arr2 = [2, 3];
// 세 번째 안수 arr2를 해체하여 전달해야 한다.
// 그렇지 않으면 arrl어/ arr2 배열 자체가 추가된다.
arrl.splice(1, 0, arr2);
// 기대한 결과는 [1, [2, 3], 4]가 아니라 [1, 2, 3, 4j다.
console.log(arrl); // [1, [2, 3], 4]
```

- 위 예제의 경우 splice 메서드의 세 번째 인수 [2, 3]을 2, 3으로 해체하여 전달해야 한다. 그렇지 않으면 arrl에 arr2 배열 자체가 추가된다.
- 스프레드 문법을 사용하면 다음과 같이 더욱 간결하고 가독성 좋게 표현할 수 있다.

```js
// ES6
const arrl = [1, 4];
const arr2 = [2, 3];
arrl.splice(l, 0, ... arr2);
console.log(arrl); // [1, 2, 3, 4]
```

##### 35.2.3 배열 복사

- ES5에서 배열을복사하려면 slice 메서드를 사용한다.

```js
// ES5
var origin = [1, 2];
var copy = origin.slice();
console.log(copy); // [1, 2]
console.log(copy === origin); // false
```

- 스프레드 문법을 사용하면 다음과 같이 더욱 간결하고 가독성 좋게 표현할 수 있다.

```js
// ES6
const origin = [1, 2];
const copy = [... origin];
console.log(copy); // [1, 2]
console.log(copy === origin); // false
```

- 이때 원본 배열의 각 요소를 얕은 복사 shallow 하여 새로운 복사본을 생성한다. 이는 slice 메서드도 마찬가지다.

##### 35.2.4 이터러블을 배열로 변환

- ES5에서 이터러블을 배열로 변환하려면 Function.prototype.apply 또는 Function.prototype.call 메서드를 사용하여 slice 메서드를 호줄해야 한다.

```js
// ES5
function sum() {
    // 이터러블이면서 유사 배열 객체인 arguments를 배열로 변환
    var args = Array.prototype.splice.call(arguments);
    return args.reduce(function (pre, cur) {
        return pre + cur;
    }, 0)；
}
console.log(sum(l, 2, 3)); // 6
```

- 스프레드 문법을 사용하면 좀 더 간편하게 이터러블을 배열로 변환할수 있다. arguments 객체는 이터러블이면서 유사 배열 객체다. 따라서 스프레드 문법의 대상이 될 수 있다.

```js
function sum() {
    // 이터러블이면서 유사 배열 객체인 arguments를 배열로 변환
    return [... arguments].reduce((pre, cur) => pre + cur, 0);
}
console.log(sum(l, 2, 3)); // 6

```

#### 35.3 객체 리터럴 내부에서 사용하는 경우

- Rest 프로퍼티와 함께 2021 년 1월 현재 TC39 프로세스의 stage 4(Finished) 단계에 제안되어 있는 스프레드 프로퍼티를 사용하면 객체 리터럴의 프로퍼티 목록에서도 스프레드 문법을 사용할 수 있다. **스프레드 문법의 대상은 이터러블이어야 하지만 스프레드 프로퍼티 제안은 일반 객체를 대상으로도 스프레드 문법의 사용을 허용**한다.

```js
// 스프레드 프로퍼티
// 객체 복사(얕은 복사)
const obj = { x: 1, y: 2 };
const copy = { ... obj };
console.log(copy); // { x: 1, y: 2 }
console.log(obj === copy); // false
// 객체 병합
const merged = { x: 1, y: 2, ... { a: 3, b: 4 } };
console.log(merged); // { x: 1, y: 2, a: 3, b: 4 }
```

<h3>끝!</h3>
