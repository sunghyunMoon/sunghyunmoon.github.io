---
title: 17장 생성자 함수에 의한 객체 생성
categories:
- Modern JavaScript DeepDive
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

이번 17 장에서는 다양한 객체 생성 방식 중에서 생성자 함수를 사용하여 객체를 생성하는 방식을 살펴본다. 그리고 객체 리터럴을 사용하여 객체를 생성하는 방식과 생성자 함수를 사용하여 객체를 생성하는 방식과의 장단점을 살펴보자!

#### 17.1 Object 생성자 함수

- new 연산자와 함께 Object 생성자 함수를 호출하면 빈 객체를 생성하여 반환한다. 빈 객체를 생성한 이후 프로퍼티 또는 메서드를 추가하여 객체를 완성할 수 있다.

```js
// 빈 객체의 생성
const person = new Object();
// 프로퍼티 추가
person.name = 'Lee';
person.sayHello = function () {
    console.log('Hi! My name is ' + this.name);
};
console.log(person); // {name: "Lee", sayHello: /}
person.sayHello(); // Hi! My name is Lee
```

- 생성자 함수(constructor)란 new 연산자와 함께 호출하여 객체(인스턴스)를 생성하는 함수를 말한다. 생성자 함수에 의해 생성된 객체를 인스턴스(instance)라 한다.
- 자바스크립트는 Object 생성자 함수 이외에도 String, Number, Boolean, Function, Array, Date, RegExp, Promise 등의 빌트인 생성자 함수를 제공한다.
- 반드시 Object 생성자 함수를 사용해 빈 객체를 생성해야 하는 것은 아니다. 객체를 생성하는 방법은 객체 리터럴을 사용하는 것이 더 간편하다. Object 생성자 함수를 사용해 객체를 생성하는 방식은 특별한 이유가 없다면 그다지 유용해 보이지 않는다.

#### 17.2 생성자 함수

##### 17.2.1 객체 리터럴에 의한 객체 생성 방식의 문제점

- 객체 리터럴에 의한 객체 생성 방식은 직관적이고 간편하다. 하지만 **객체 리터럴에 의한 객체 생성 방식은 단하나의 객체만 생성**한다. 따라서 동일한 프로퍼티를 갖는 객체를 여러 개 생성해야 하는 경우 매번 같은 프로퍼티를 기술해야 하기 때문에 비효율적이다.
- 객체는 프로퍼티를 통해 객체 고유의 상태(state)를 표현한다. 그리고 메서드를 통해 상태 데이터인 프로퍼티를 참조하고 조작하는 동작을 표현한다. 따라서 프로퍼티는 객체마다 프로퍼티 값이 다를 수 있지만 메서드는 내용이 동일한 경우가 일반적이다.

##### 17.2.2 생성자 함수에 의한 객체 생성 방식의 장점

- **생성자 함수에 의한 객체 생성 방식은 마치 객체(인스턴스)를 생성하기 위한 템플릿(클래스)처럼 생성자 함수를 사용하여 프로퍼티 구조가 동일한 객체 여러 개를 간편하게 생성**할 수 있다.

```js
// 생성자 함수
function Circle(radius) {
    // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    }；
}
// 인스턴스의 생성
const circle1 = new Circle(5); // 반지름이 5인 Circle 객체를 생성
const circle2 = new Circle( 10); // 반지름이 10인 Circle 객체를 생성
console.log(circle1.getDiameter()); // 10
console.Iog(circle2.getDiameter()); // 20

```

- 생성자 함수는 이름 그대로 객체(인스턴스)를 생성하는 함수다. 하지만 자바와 같은 클래스 기반 객체지향 언어의 생성자와는 다르게 그 형식이 정해져 있는 것이 아니라 **일반 함수와 동일한 방법으로 생성자 함수를 정의하고 new 연산자와 함께 호출하면 해당 함수는 생성자 함수로 동작**한다. 만약 new 연산자와 함께 생성자 함수를 호출하지 않으면 생성자 함수가 아니라 일반 함수로 동작한다.

```js
// new 연산자와 함께 호출하지 않으면 생성자 함수로 동작하지 않는다.
// 즉, 일반 함수로서 호출된다.
const circles = Circle(15);
// 일반 함수로서 호출된 Circle은 반환문이 없0므루 암묵적으로 undefine% 반환한다.
console.Iog(circle3); // undefined
// 일반 함수로서 호출된 Circle 내의 this는 전역 객체를 가리킨다.
console.log(radius); // 15
```

##### 17.2.3 생성자 함수의 인스턴스 생성 과정

- 생성자 함수의 역할은 **프로퍼티 구조가 동일한 인스턴스를 생성하기 위한 템플릿(클래스)으로서 동작하여 인스턴스를 생성**하는 것과 **생성된 인스턴스를 초기화**(인스턴스 프로퍼티 추가 및 초기값 할당)하는 것이다. 

```js
// 생성자 함수
function Circle(radius) {
    // 인스턴스 초기화
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    }；
}
// 인스턴스 생성
const circlel = new Circle（5）; // 반지름이 5인 Circle 객체를 생성
```

- 생성자 함수 내부의 코드를 살펴보면 this에 프로퍼티를 추가하고 필요에 따라 전달된 인수를 프로퍼티의 초기값으로서 할당하여 인스턴스를 초기화한다. 하지만 인스턴스를 생성하고 반환하는 코드는 보이지 않는다.
- 자바스크립트 엔진은 암묵적인 처리를 통해 인스턴스를 생성하고 반환한다. **new 연산자와 함께 생성자 함수를 호출하면 자바스크립트 엔진은 다음과 같은 과정을 거쳐 암묵적으로 인스턴스를 생성하고 인스턴스를 초기화한 후 암묵적으로 인스턴스를 반환**한다.

###### 인스턴스 생성과 this 바인딩

- 암묵적으로 빈 객체가 생성된다. 이 빈 객체가 바로（아직 완성되지는 않았지만） 생성자 함수가 생성한 인스턴스다. 그리고 암묵적으로 생성된 **빈 객체, 즉 인스턴스는 this에 바인딩**된다. 생성자 함수 내부의 this가 생성자 함수가 생성할 인스턴스를 가리키는 이유가 바로 이것이다. 이 처리는 함수 몸체의 코드가 한 줄씩 실행되는 **런타임 이전에 실행**된다.

```js
function Circle（radius） {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
    console.log（this）; // Circle {}
    this.radius = radius;
    this.getDiameter = function （） {
        return 2 * this.radius;
    }；
}
```

###### 인스턴스 초기화

- 생성자 함수에 기술되어 있는 **코드가 한 줄씩 실행되어 this에 바인딩되어 있는 인스턴스를 초기화**한다. 즉, this에 바인딩되어 있는 인스턴스에 프로퍼티나 메서드를 추가하고 생성자 함수가 인수로 전달받은 초기값을 인스턴스 프로퍼티에 할당하여 초기화하거나 고정값을 할당한다. 이 처리는 개발자가 기술한다.

###### 인스턴스 반환

- 생성자 함수 내부의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
- 만약 this가 아닌 다른 객체를 명시적으로 반환하면 this가 반환되지 못하고 return 문에 명시한 객체가 반환된다.
- 하지만 명시적으로 원시 값을 반환하면 원시 값 반환은 무시되고 암묵적으로 this가 반환된다.

```js
function Circle(radius) {
    // 1. 암묵적으로 빈 객체가 생성되고 thisOfl 바인딩된다.
    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    }；
    // 3. 암묵적으로 this를 반환한다.
    // 명시적으로 원시 값을 반환하면 원시 값 반환은 무시되고 암묵적으로 this가 반환된다.
    return 100;
}
// 인스턴스 생성. Circle 생성자 함수는 명시적으로 반환한 객체를 반환한다.
const circle = new Circle(l);
console.log(circle); // Circle {radius: 1, getDiameter: f}
```

- 이처럼 생성자 함수 내부에서 명시적으로 this가 아닌 다른 값을 반환하는 것은 생성자 함수의 기본 동작을 훼손한다. 따라서 **생성자 함수 내부에서 return 문을 반드시 생략**해야 한다.

##### 17.2.4  내부 메서드 [[Call]]과 [[Construct]]

- **함수 선언문 또는 함수 표현식으로 정의한 함수는 일반적인 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다.** 생성자 함수로서 호출한다는 것은 new 연산자와 함께 호출하여 객체를 생성하는 것을 의미한다.
- 함수는 객체이므로 일반 객체와 동일하게 동작할 수 있다. 함수 객체는 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드를 모두 가지고 있기 때문이다.

```js
// 함수는 객체다.
function foo() {}
// 함수는 객체이므로 프로퍼티를 소유할 수 있다.
foo.prop = 10;
// 함수는 객체이므로 메서드를 소유할 수 있다.
foo.method = function () {
    console.log(this.prop);
}；
foo.method(); // 10
```

- 함수는 객체이지만 일반 객체와는 다르다. **일반 객체는 호출할 수 없지만 함수는 호출할 수 있다.** 따라서 함수 객체는 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드는 물론, 함수로서 동작하기 위해 함수 객체만을 위한 [[Environment]], [[FormalParameters]] 등의 내부 슬롯과 [[Call]], [[Construct]] 같은 내부 메서드를 추가로 가지고 있다. 함수가 일반 함수로서 호출되면 함수 객체의 내부 메서드 [ [Call] ]이 호출되고 new 연산자와 함께 생성자 함수로서 호출되면 내부 메서드 [[Construct]]가 호출된다.

```js
function foo() {}
// 일반적인 함수로서 호출: [[Call]]이 호출된다.
foo();
// 생성자 함수로서 호출: [[Construct]]가 호출된다.
new foo();
```

- 내부 메서드 [[Call]]을 갖는 함수 객체를 callable이라 하며, 내부 메서드 [[Construct]]를 갖는 함수 객체를 constructor, [ [Construct] ]를 갖지 않는 함수 객체를 non—constructor라고 부른다. callable은 호출할 수 있는 객체, 즉 함수를 말하며, constructor는 생성자 함수로서 호출할 수 있는 함수, non-constructor는 객체를 생성자 함수로서 호출할 수 없는 함수를 의미한다.
- 호출할 수 없는 객체는 함수 객체가 아니므로 **함수로서 기능하는 객체, 즉 함수 객체는 반드시 callable**이어야 한다. 따라서 모든 함수 객체는 내부 메서드 [[Call]]을 갖고 있으므로 호출할 수 있다. 하지만 모든 함수 객체가 [[Construct]]를 갖는 것은 아니다. 다시 말해, **함수 객체는 constructor일 수도 있고 nonconstructor일 수도 있다.**

##### 17.2.5 constructor와 non-constructor의 구분

- 자바스크립트 엔진이 어떻게 constructor와 non-constructor를 구분하는지 살펴보자. **자바스크립트 엔진은 함수 정의를 평가하여 함수 객체를 생성할 때 함수 정의 방식에 따라 함수를 constructor와 nonconstructor로 구분**한다.
    - constructor： 함수 선언문. 함수 표현식, 클래스(클래스도 함수다)
    - non-constructor： 메서드(ES6 메서드 축약 표현), 화살표 함수
- 이때 주의할 것은 ECMAScript 사양에서 메서드로 인정하는 범위가 일반적인 의미의 메서드보다 좁다는 것이다. 다음 예제를 살펴보자.

```js
// 일반 함수 정의: 함수 선언문, 함수 표현식
function foo() {}
const bar = function () {};
// 일반 함수로 정의된 함수만이 constructor다.
new foo(); // — foo {}
new bar(); // — bar {}
// 화살표 함수 정의
const arrow = () => {};
new arrow(); // TypeError: arrow is not a constructor
```

- 함수가 어디에 할당되어 있는지에 따라 메서드인지를 판단하는 것이 아니라 **함수 정의 방식에 따라 constructor와 non-constructor를 구분**한다. 따라서 위 예제와 같이 일반 함수, 즉 함수 선언문과 함수 표현식으로 정의된 함수만이 constructor이고 ES6의 화살표 함수와 메서드 축약 표현으로 정의된 함수는 non-constructor다.

##### 17.2.6 new 연산자

- 일반 함수와 생성자 함수에 특별한 형식적 차이는 없다. new 연산자와 함께 함수를 호출하면 해당 함수는 생성자 함수로 동작한다. 다시 말해, 함수 객체의 내부 메서드 [[Call]]이 호출되는 것이 아니라 [[Construct]]가 호출된다. 단, **new 연산자와 함께 호출하는 함수는 non—constructor가 아닌 constructor 이어야 한다.**

```js
// 생성자 함수로서 정의하지 않은 일반 함수
function add(x, y) {
    return x + y;
}
// 생성자 함수로서 정의하지 않은 일반 함수를 new 연산자와 함께 호출
let inst = new add();
// 함수가 객체를 반환하지 않았.으므루 반환문이 무시된다. 따라서 빈 객체가 생성되어 반환된다.
console.log(inst); // {}
// 객체를 반환하는 일반 함수
function createUser(name, role) {
    return { name, role };
}
// 일반 함수를 new 연산자와 함께 호출
inst = new createUser(1 Lee', 'admin1);
// 함수가 생성한 객체를 반환한다.
console.log(inst); // {name: "Lee", role: "admin"}
```

- 반대로 new 연산자 없이 생성자 함수를 호출하면 일반 함수로 호출된다. 다시 말해, 함수 객체의 내부 메서드[[Construct]]가 호출되는 것이 아니라 [[Call]]이 호출된다.

##### 17.2.7 new.target

- 생성자 함수가 new 연산자 없이 호출되는 것을 방지하기 위해 파스칼 케이스 컨벤션을 사용한다 하더라도 실수는 언제나 발생할 수 있다. 이러한 위험성을 회피하기 위해 ES6에서는 new.target을 지원한다.
- new.target은 this와 유사하게 constructor인 모든 함수 내부에서 암묵적인 지역 변수와 같이 사용되며 메타 프로퍼티라고 부른다. 참고로 IE는 new.target을 지원하지 않으므로 주의하기 바란다.
- 함수 내부에서 new.target을 사용하면 new 연산자와 함께 생성자 함수로서 호출되었는지 확인할 수 있다. **new 연산자와 함께 생성자 함수로서 호출되면 함수 내부의 new.target은 함수 자신**을 가리킨다. **new 연산자 없이 일반 함수로서 호출된 함수 내부의 new.target은 undefined**다.
- 따라서 함수 내부에서 new.target을 사용하여 new 연산자와 생성자 함수로서 호출했는지 확인하여 그렇지 않은 경우 new 연산자와 함께 재귀 호출을 통해 생성자 함수로서 호출할 수 있다.

```js
// 생성자 함수
function Circle(radius) {
    // 이 함수가 new 연산자와 함께 호출되지 않았다면 new. target은 undefined다.
    if (!new.target) {
        // new 연산자와 함께 생성자 함수를 재귀 호출하여 생성된 인스턴스를 반환한다.
        return new Circle(radius);
    }
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    }；
}
// new 연산자 없이 생성자 함수를 호출하여도 new. target을 통해 생성자 함수로서 호출된다.
const circle = Circle(5);
console.log(circle.getDiameter());
```

<h3>끝!</h3>