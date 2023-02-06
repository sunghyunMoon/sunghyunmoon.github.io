---
title: 22장 this
categories:
- Modern JavaScript DeepDive
feature_image: "/assets/img/background/javascript.PNG"
---

자바 스크립트에서의 this에 대해서 알아보자.

<h3>22.1 this 키워드</h3>

- 객체는 상태(state)를 나타내는 프로퍼티와 동작을 나타내는 메서드를 하나의 논리적인 단위로 묶은 복합적인 자료구조다.

``` js
const circle = {
// 프로퍼티: 객체 고유의 상태 데이터 
radius: 5,
// 메서드: 상태 데이터를 참조하고 조작하는 동작 
getDiameter() {
// 이 메서드가 자신이 속한 객체의 프로퍼티나 다른 메서드를 참조하려면
// 자신이 속한 객체인 circle을 참조할 수 있어야 한다.
return 2 * circle.radius; 
    }
}；
console.log(circle.getDiameter()); // 10
```
- 동작을 나타내는 메서드는 자신이 속한 객체의 상태, 즉 프로퍼티를 참조하고 변경할 수 있어야 한다. 이때 메서드가 자신이 속한 객체의 프로퍼티를 참조하려면 먼저 **자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다.**
객체 리터럴 방식으로 생성한 객체의 경우 메서드 내부에서 메서드 자신이 속한 객체를 가리키는 식별자를 재귀적으로 참조할 수 있다.
- 위 예제와 같이 **객체 리터럴 방식**으로 생성한 객체의 경우 메서드 내부에서 메서드 자신이 속한 객체를 가리키는 식별자를 재귀적으로 참조할 수 있다. 하지만 자기 자신이 속한 객체를 재귀적으로 참조하는 방식은 일반적이지 않으며 바람직하지도 않다. 
- **this는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수self-referencing variable다. this 를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.**
- 자바나 C++ 같은 클래스 기반 언어에서 this는 언제나 클래스가 생성하는 인스턴스를 가리킨다. **하지만 자바스크립트의 this는 함수가 호출되는 방식에 따라 this에 바인딩될 값, 즉 this 바인딩이 동적으로 결정된다.**
- this는 코드 어디에서든 참조 가능하다. 전역에서도 함수 내부에서도 참조할 수 있다.

``` js
// this는 어디서든지 참조 가능하다.
// 전역에서 this는 전역 객체 window를 가리킨다.
console.log(this); // window
function square(number) {
    // 일반 함수 내부에서 this는 전역 객체 window를 가리킨다.
    console.log(this); // window
    return number * number;
}
square(2);
    const person = {
    name: 'Lee',
    getName() {
    // 메서드 내부에서 this는 메서드를 호출한 객체를 가리킨다,
    console.log(this); // {name: "Lee", getName: f}
    return this.name;
    }
}；
console.log(person.getName()); // Lee
    function Person(name) {
    this.name = name;
    // 생성자 함수 내부에서 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
    console.log(this); // Person {name: "Lee"}
}
const me = new Person('Lee');
```

- 하지만 this는 객체의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수이므로 **일반적으로 객체의 메서드 내부 또는 생성자 함수 내부에서만 의미가 있다.**


<h3>22.2 함수 호출 방식과 this 바인딩</h3>

- 위에서 말했지만, 자바스크립트에서의 this는 함수 호출 방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다. 자바스크립트에서는 함수도 다양한 방식으로 호출할 수 있다. 함수를 호출하는 방식은 아래와 같다.

1.	일반 함수 호출
2.	메서드호출
3.	생성자 함수 호출
4.	Function.prototype.apply/call/bind 메서드에 의한 간접 호출

<h4>22.2.1 일반 함수 호출</h4>

- **기본적으로 this에는 전역 객체global object가 바인딩된다.**

``` js
function foo() {
    console.log("foo's this: ", this); // window
    function bar() {
    console.log("bar's this: ", this); // window
    }
    bar();
}
foo();
```

- 위 예제처럼 전역 함수는 물론이고 중첩 함수를 **일반 함수로 호출하면 함수 내부의 this에는 전역 객체가 바인딩된다.**
- **일반 함수로 호출된 모든 함수(중첩 함수, 콜백 함수 포함) 내부의 this에는 전역 객체가 바인딩된다.**

<h4>22.2.2 메서드 호출</h4>

- 메서드 내부의 this에는 메서드를 호출한 객체. 즉 메서드를 호출할 때 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체가 바인딩된다. 주의할 것은 **메서드 내부의 this는** 메서드를 소유한 객체가 아닌 **메서드를 호출한 객체에 바인딩**된다는 것이다. 

<h4>22.2.3 생성자 호출</h4>

- 생성자 함수 내부의 this에는 생성자 함수가 (미래에) 생성할 인스턴스가 바인딩된다.
- C++에서와 같으니 패스!

<h4>22.2.4 Function.prototype.apply/call/bind 메서드에 의한 간접 호출</h4>

- apply, call, bind 메서드는 Function.prototype의 메서드다. 즉, 이들 메서드는 모든 함수가 상속받아 사용 할 수있다.
- Function. prototype .apply. Function. prototype. call 메서드는 this로 사용할 객체와 인수 리스트를 인수 로 전달받아 **함수를 호출**한다. apply와 call 메서드의 사용법은 다음과 같다.

``` js
/**
* 주어진 this 바인딩과 인수 리스드 배열을 사용하여 함수를 호촐한다.
* @param thisArg - this로 사용할 객체
* @param argsArray - 함수에게 전달할 인수 리스트의 배열 또는 유사 배열 객체
* ©returns 호출된 함수의 반환값
*/
Function.prototype.apply(thisArgt, argsArray])
/**
* 주어진 this 바인딩과，로 구분된 인수 리스트를 사용하여 함수를 호출한다.
* @param thisArg - this로 사용할 객체
* @param argl, arg2, ... - 함수에게 전달할 인수 리스트
* ©returns 호출된 함수의 반환값
*/
Function.prototype.call (thisArg[, argl[, arg2[, ... ]]])

```

- apply와 call 메서드의 본질적인 기능은 함수를 호출하는 것이다. apply와 call 메서드는 함수를 호출하면 서 첫 번째 인수로 전달한 특정 객체를 호출한 함수의 this에 바인딩한다.
- **apply와 call 메서드는 호출할 함수에 인수를 전달하는 방식만 다를 뿐 동일하게 동작한다.**

``` js
function getThisBinding() {
    console.log(arguments);
    return this;
}
// this로 사용할 객체
const thisArg = { a： 1 };
// getThisBinding 함수를 호출하면서 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
// apply 메서드는 호출할 함수의 인수를 배열로 묶어 전달한다.
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// Arguments(3) [1, 2, 3, callee: f, Symbol(Symbol.iterator)： f]
// {a: 1}
// call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) fl, 2, 3, callee: f, Symbol(5ymbol.iterator)： f]
// {a: 1}

```

- apply 메서드는 호출할 함수의 인수를 배열로 묶어 전달한다. call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다. 
- 이처럼 **apply와 call 메서드는 호출할 함수에 인수를 전달하는 방식만 다 를 뿐 this로 사용할 객체를 전달하면서 함수를 호출하는 것은 동일**하다.
- **Function.prototype.bind 메서드는** apply와 call 메서드와 달리 함수를 호출하지 않는다. 다만 **첫 번째 인 수로 전달한 값으로 this 바인딩이 교체된 함수를 새롭게 생성**해 반환한다.

``` js
function getThisBinding() {
    return this;
}
// this로 사용할 객체
const thisArg = { a: 1 };
// bind 메서드는 첫 번째 인수로 전달한 thisArg로 this 바인딩이 교체된
// getThisBinding 함수를 새롭게 생성해 반환한다.
console.log(getThisBinding.bind(thisArg)); // getThisBinding
// bind 메서드는 함수를 호출하지는 않으므로 명시적으로 호출해야 한다.
console.log(getThisBinding.bind(thisArg)()); // {a: 1}

```

- bind 메서드는 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결 하기 위해 유용하게 사용된다.

``` js
const person = {
    name: 'Lee' ,
    foo(callback) {
        // 1
        setTimeout(callback, 100);
    }
};
person.foo(function () {
    console.log( 'Hi! my name is ${this.name}.' );  // 2
}

```

- person.foo의 콜백 함수가 호출되기 이전인 1의 시점에서 this는 **foo 메서드를 호출한 객체**, 즉 person 객체를 가리킨다. 
- 그러나 person.foo의 콜백 함수가 **일반 함수로서 호출**된 2의 시점에서 this는 전역 객체 window를 가리킨다. 
- 따라서 콜백 함수 내부의 this를 외부 함수 내부의 this와 일치시켜야 한다. 이때 bind 메서드를 사용하여 this를 일치시킬 수 있다.

``` js
const person = {
    name: 'Lee' ,
    foo(callback) {
        // bind 메서드로 callback 함수 내부의 this 바인딩
        setTimeout(callback.bind(this), 100);
    }
};
person.foo(function () {
    console.log( 'Hi! my name is ${this.name}.' );  // Hi! my name is Lee.
}

```

- 정리!
<div><img src= "/assets/img/post/this_binding.PNG"></div>


<h3>끄~ㅌㅌㅌ!</h3>
