---
title: 34장 이터러블
categories:
- Modern JavaScript DeepDive
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

#### 34.1 이터레이션 프로토콜

- **ES6에서 도입된 이터레이션 프로토콜(iteration protocol)은 순회 가능한(iterable) 데이터 컬렉션（자료구조）을 만들기 위해 ECMAScript 사양에 정의하여 미리 약속한 규칙**이다.
- ES6 이전의 순회 가능한 데이터 컬렉션, 즉 배열, 문자열, 유사 배열 객체, DOM 컬렉션 등은 통일된 규약 없이 각자 나름의 구조를 가지고 for 문, for... in 문, forEach 메서드 등 다양한 방법으로 순회할 수 있었다.
- ES6에서는 순회 가능한 데이터 컬렉션을 이터레이션 프로토콜을 준수하는 이터러블로 통일하여 for... of문, 스프레드 문법, 배열 디스트럭처링 할당의 대상으로 사용할 수 있도록 일원화했다.
- **이터레이션 프로토콜에는 이터러블 프로토콜과 이터레이터 프로토콜**이 있다.
- 이터러블 프로토콜 : 이터러블 프로토콜을 준수한 객체를 이터러블이라 한다. 이터러블은 for... of 문으로 순회할 수 있으며 스프레드 문법과 배열 디스트럭처링 할당의 대상으로 사용할 수 있다.
- 이터레이터 프로토콜 : 이터레이터는 next 메서드를 소유하며 next 메서드를 호출하면 이터러블을 순회하며 value와 done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환한다. 이러한 규약을 이터레이터 프로토콜이라 하며, 이터레이터 프로토콜을 준수한 객체를 이터레이터라 한다. **이터레이터는 이터러블의 요소를 탐색하기 위한 포인터 역할**을 한다.

#### 34.1.1 이터러블

- **이터러블 프로토콜을 준수한 객체를 이터러블이라 한다. 즉, 이터러블은 Symbol.iterator를 프로퍼티 키로 사용한 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속받은 객체**를 말한다.
- 예를 들어, 배열은 Array.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.

```js
const array = [1, 2, 3];
// 배열은 Array.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.
console.log(Symbol.iterator in array); // true
// 이터러블인 배열은 for... of 문으로 순회 가능하다.
for (const item of array) { 
    console.log(item);
}
// 이터러블인 배열은 스프레드 문법의 대상으로 사용할 수 있다.
console.log([ ...array]); // 〔1, 2, 3]
// 이터러블인 배열은 배열 디스트럭처링 할당의 대상으로 사용할 수 있다.
const [a, ... rest] = array;
console.log(a, rest); // 1, [2, 3]
```

- Symbol.iterator 메서드를 직접 구현하지 않거나 상속받지 않은 일반 객체는 이터러블 프로토콜을 준수한 이터러블이 아니다. 따라서 **일반 객체는 for... of 문으로 순회할 수 없으며 스프레드 문법과 배열 디스트럭처링 할당의 대상으로 사용할 수 없다.**

```js
const obj = { a: 1, b: 2 };
// 일반 객체는 Symbol.iterator 메서드를 구현하거나 상속받지 않는다.
// 따라서 일반 객체는 이터러블 프로토콜을 준수한 이터러블이 아니다.
console.log(Symbol.iterator in obj); // false
// 이터러블이 아닌 일반 객체는 for... of 문으로 순회할 수 없다.
for (const item of obj) { // — TypeError: obj is not iterable
    console.log(item);
}
// 이터러블이 아닌 일반 객체는 배열 디스트럭처링 할당의 대상으로 사용할 수 없다.
const [a, b] = obj; // — TypeError: obj is not iterable
```

- 단, 2021 년 1 월 현재, TC39 프로세스의 stage 4(Finished) 단계에 제안되어 있는 스프레드 프로퍼티 제안은 일반 객체에 스프레드 문법의 사용을 허용한다.

```js
const obj = { a: 1, b: 2 };
// 스프레드 프로퍼티 제안(Stage 4)은 객체 리터럴 내부에서 스프러/드 문법의 사용을 허용한다.
console.log({ ... obj }); // { a: 1, b: 2 }
```

- 하지만 일반 객체도 이터러블 프로토콜을 준수하도록 구현하면 이터러블이 된다. 이에 대해서는 34.6절 “사용자 정의 이터러블”에서 살펴보도록 하자.

#### 34.1.2 이터레이터

- 이터러블의 Symbol.iterator 메서드를 호줄하면 이터레이터 프로토콜을 준수한 이터레이터를 반환한다. **이터러블의 Symbol.iterator 메서드가 반환한 이터레이터는 next 메서드를 갖는다.**
- 이터레이터의 next 메서드는 이터러블의 각 요소를 순회하기 위한 포인터의 역할을 한다. 즉, next 메서드를 호출하면 이터러블을 순차적으로 한 단계씩 순회하며 순회 결과를 나타내는 이터레이터 리절트 객체를 반환한다.

```js
// 배열은 이터러블 프로토콜을 준수한 이터러블이다.
const array =[1,2,3];
// Symbol.iterator 메서드는 이터레이터를 반환한다. 이터레이터는 next 메서드를 갖는다.
const iterator = array[Symbol.iterator]();
// next 메서드를 호출하면 이터러블을 순회하며 순회 결과를 나타내는 이터레이터 리절트 객체를 반환한다.
// 이터레이터 리절트 객체는 value와 done 프로퍼티를 갖는 객체다.
console.log(iterator.next())
console.log(iterator.next())
console.log(iterator.next())
console.log(iterator.next())
// { value: 1, done: false }
// { value: 2, done: false }
// { value: 3, done: false }
// { value: undefined, done: true }
```

- 이터레이터의 next 메서드가 반환하는 이터레이터 리절트 객체의 value 프로퍼티는 현재 순회 중인 이터러블의 값을 나타내며 done 프로퍼티는 이터러블의 순회 완료 여부를 나타낸다.

#### 34.2 빌트인 이터러블

- **자바스크립트는 이터레이션 프로토콜을 준수한 객체인 빌트인 이터러블을 제공**한다. 다음의 표준 빌트인 객체들은 빌트인 이터러블이다.

<div><img src= "/assets/img/post/builtin_iterable1.PNG"></div>

<div><img src= "/assets/img/post/builtin_iterable2.PNG"></div>

#### 34.3 for...of 문

- **for... of 문은 이터러블을 순회하면서 이터러블의 요소를 변수에 할당**한다. for... of 문의 문법은 다음과 같다.

for （변수선언문 of 이터러블） { ... }\

- for...of 문은 내부적으로 이터레이터의 next 메서드를 호출하여 이터러블을 순회하며 **next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티 값을 for... of 문의 변수에 할당**한다. 그리고 이터레이터 리절트 객체의 done 프로퍼티 값이 false이면 이터러블의 순회를 계속하고 true이면 이터러블의 순회를 중단한다.
- for... of 문의 내부 동작을 for 문으로 표현하면 다음과 같다.

```js
// 이터러블
const iterable = [1, 2, 3];
// 이터러블의 Symbol.iterator 메서드를 호출하여 이터레이터를 생성한다.
const iterator = iterable[Symbol.iterator]();
for (;; ) {
    // 이터레이터의 next 메서드를 호출하여 이터러블을 순회한다.
    // 이때 next 메서드는 이터레이터 리절트 객체를 반환한다.
    const res = iterator.next();
    // next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 true이면 이터러블의 순회를 중단한다.
    if (res.done) break;
    // 이터레이터 리절트 객체의 value 프로퍼티 값을 item 변수에 할당한다.
    const item = res.value;
    console.log(item); // 1 2 3
}
```

#### 34.4 이터러블과 유사 배열 객체

- **유사 배열 객체는 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있고 length 프로퍼티를 갖는 객체**를 말한다. 유사 배열 객체는 length 프로퍼티를 갖기 때문에 for 문으로 순회할 수 있고, 인덱스를 나타내는 숫자 형식의 문자열을 프로퍼티 키로 가지므로 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있다.

```js
// 유사 배열 객체
const arrayLike = {
0： 1,
1： 2,
2： 3,
length: 3
// 유사 배열 객체는 length 프로퍼티를 갖기 때문에 for 문으로 순회할 수 있다.
for (let i = 0; i < arrayLike.length; i++) {
    // 유사 배열 객체는 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있다.
    console.log(arrayLike[i]); // 1 2 3
}
```

- **유사 배열 객체는 이터러블이 아닌 일반 객체**다. 따라서 유사 배열 객체에는 Symbol.iterator 메서드가 없기 때문에 for... of 문으로 순회할 수 없다.
- 단, arguments, NodeList, HTMLCollection은 유사 배열 객체이면서 이터러블이다. 정확히 말하면 ES6에서 이터러블이 도입되면서 유사 배열 객체인 arguments, NodeList, HTMLCollection 객체에 Symbol, iterator 메서드를 구현하여 이터러블이 되었다. 하지만 이터러블이 된 이후에도 length 프로퍼티를 가지며 인덱스로 접근할 수 있는 것에는 변함이 없으므로 유사 배열 객체이면서 이터러블인 것이다.
- **배열도 마찬가지로 ES6에서 이터러블이 도입되면서 Symbol.iterator 메서드를 구현하여 이터러블이 되었다.**
- 하지만 모든 유사 배열 객체가 이터러블인 것은 아니다. 위 예제의 arrayLike 객체는 유사 배열 객체지만 이터러블이 아니다. 다만 **ES6에서 도입된 Array.from 메서드를 사용하여 배열로 간단히 변환할 수 있다.** Array.from 메서드는 유사 배열 객체 또는 이터러블을 인수로 전달받아 배열로 변환하여 반환한다.

```js
// 유사 배열 객체
const arrayLike = {
0： 1,
1： 2,
2： 3,
length: 3
}；
// Array, fro까은 유사 배열 객체 또는 이터러블을 배열로 변환한다.
const arr = Array.from(arrayLike);
console.log(arr); // [1, 2, 3]
```

#### 34.5 이터레이션 프로토콜의 필요성

- ES6에서는 순회 가능한 데이터 컬렉션을 이터레이션 프로토콜을 준수하는 이터러블로 통일하여 **for...of 문, 스프레드 문법, 배열 디스트럭처링 할당의 대상으로 사용할수 있도록 일원화** 했다.
- 이터러블은 for ... Of 문, 스프레드 문법, 배열 디스트럭처링 할당과 같은 데이터 소비자(data consumer)에 의해 사용되므로 데이터 공급자(data Provider)의 역할을 한다고 할 수 있다.
- 만약 다양한 데이터 공급자가 각자의 순회 방식을 갖는다면 데이터 소비자는 다양한 데이터 공급자의 순회 방식을 모두 지원해야 한다. 이는 효율적이지 않다. 하지만 다양한 데이터 공급자가 이터레이션 프로토콜을 준수하도록 규정하면 데이터 소비자는 이터레이션 프로토콜만 지원하도록 구현하면 된다.
- 이처럼 이터레이션 프로토콜은 다양한 데이터 공급자가 하나의 순회 방식을 갖도록 규정하여 데이터 소비자가 효율적으로 다양한 데이터 공급자를 사용할 수 있도록 **데이터 소비자와 데이터 공급자를 연결하는 인터페이스의 역할**을 한다.


<h3>끄~ㅌㅌㅌ!</h3>
