---
title: 25장 클래스
categories:
- Modern JavaScript DeepDive
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

이번 장에서는 자바스크립트에서의 클래스에 대해 살펴보고, 타입스크립트와의 차이점에 대해서도 추후 생각해보겠다.

#### 25.1 클래스는 프로토타입의 문법적 설탕인가?

- **자바스크립트는 프로토타입 기반(Prototype based) 객체지향 언어**다. 비록 다른 객체지향 언어와의 차이점에 대한 논쟁이 있긴 하지만 자바스크립트는 강력한 객체지향 프로그래밍 능력을 지니고 있다.
- **프로토타입 기반 객체지향 언어는 클래스가 필요 없는 객체지향 프로그래밍 언어**다. ES5에서는 클래스 없이도 다음과 같이 생성자 함수와 프로토타입을 통해 객체지향 언어의 상속을 구현할 수 있다.

```js
// ES5 생성자 함수
var Person = (function () {
    // 생성자 함수
    function Person(name) {
    this.name = name;
    }
    // 프로토타입 메서드
    Person.prototype.sayHi = function () {
    console.log('Hi! My name is ' + this.name);
    }；
    // 생성자 함수 반환
    return Person;
}())；
// 인스턴스 생성
var me = new Person( Lee );
me.sayHi(); // Hi! My name is Lee
```

- **하지만 클래스 기반 언어에 익숙한 프로그래머들은 프로토타입 기반 프로그래밍 방식에 혼란**을 느낄 수 있으며, 자바스크립트를 어렵게 느끼게 하는 하나의 장벽처럼 인식되었다.
- ES6에서 도입된 클래스는 기존 프로토타입 기반 객체지향 프로그래밍보다 자바나 C#과 같은 클래스 기반 객체지향 프로그래밍에 익숙한 프로그래머가 더욱 빠르게 학습할 수 있도록 클래스 기반 객체지향 프로그래밍 언어와 매우 흡사한 새로운 객체 생성 메커니즘을 제시한다.
- 그렇다고 ES6의 클래스가 기존의 프로토타입 기반 객체지향 모델을 폐지하고 새롭게 클래스 기반 객체지향 모델을 제공하는 것은 아니다. **사실 클래스는 함수이며 기존 프로토타입 기반 패턴을 클래스 기반 패턴처럼 사용할 수 있도록 하는 문법적 설탕'syntactic sugar'**이 라고 볼 수도 있다.
- 클래스는 생성자 함수와 매우 유사하게 동작하지만 다음과 같이 몇 가지 차이가 있다.
    - 클래스를 new 연산자 없이 호출하면 에러가 발생한다. 하지만 생성자 함수를 new 연산자 없이 호출하면 일반 함수로서 호출된다.
    - 클래스는 상속을 지원하는 extends와 super 키워드를 제공한다. 하지만 생성자 함수는 extends와 super 키워드를 지원하지 않는다.
    - 클래스는 호이스팅이 발생하지 않는 것처럼 동작한다. 하지만 함수 선언문으로 정의된 생성자 함수는 함수 호이스팅이, 함수 표현식으로 정의한 생성자 함수는 변수 호이스팅이 발생한다.
    - 클래스 내의 모든 크드에는 암묵적으로 strict mode가 지정되어 실행되며 strict mode를 해제할 수 없다. 하지만 생성자 함수는 암묵적으로 strict mode가 지정되지 않는다.
    - 클래스의 constructor, 프로토타입 메서드, 정적 메서드는 모두 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 false다. 다시 말해, 열거되지 않는다.
- 생성자 함수와 클래스는 프로토타입 기반의 객체지향을 구현했다는 점에서 매우 유사하다. 하지만 **클래스는 생성자 함수 기반의 객체 생성 방식보다 견고하고 명료하다**（그렇다고 클래스가 자바스크립트의 다른 객체 생성 방식보다 우월하다고 생각하지는 않는다）. 특히 클래스의 extends와 super 키워드는 상속 관계 구현을 더욱 간결하고 명료하게 한다.
- 따라서 **클래스를 프로토타입 기반 객체 생성 패턴의 단순한 문법적 설탕이라고 보기보다는 새로운 객체 생성 메커니즘**으로 보는 것이 좀 더 합당하다.

#### 25.2 클래스 정의

- 클래스는 class 키워드를 사용하여 정의한다. 일반적이지는 않지만 함수와 마찬가지로 표현식으로 클래스를 정의할 수도 있다. 이때 클래스는 함수와 마찬가지로 이름을 가질 수도 있고, 갖지 않을 수도 있다.

```js
// 클래스 선언문
class Person {}
// 익명 클래스 표현식
const Person = class {};
// 기명 클래스 표현식
const Person = class MyClass {};

```

- **클래스를 표현식으로 정의할 수 있다는 것은 클래스가 값으로 사용할 수 있는 일급 객체라는 것을 의미**한다. 즉, 클래스는 일급 객체로서 다음과 같은 특징을 갖는다.

    - 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
    - 변수나 자료구조（객체, 배열 등）에 저장할 수 있다.
    - 함수의 매개변수에게 전달할 수 있다.
    - 함수의 반환값으로 사용할 수 있다.

- 좀 더 자세히 말하자면 클래스는 함수다. 따라서 클래스는 값처럼 사용할 수 있는 일급 객체다. 이에 대해서는 차차 알아보도록 하자.
- 클래스 몸체에는 0개 이상의 메서드만 정의할 수 있다. 클래스 몸체에서 정의할 수 있는 메서드는 constructor（생성자）, 프로토타입 메서드, 정적 메서드의 세 가지가 있다.

```js
// 클래스 선언문
class Person {
// 생성자
constructor（name） {
    // 인스턴스 생성 및 초기화
    this.name = name; // name 퍼티는 public하다.
}
// 프로토타입 메서드
sayHi() {
    console.log('Hi! My name is ${this.name}');
}
// 정적 머/서드
static sayHello() {
    console.log('Hello! );
}
}
// 인스턴스 생성
const me = new Person('Lee');
// 인스턴스의 프로퍼티 참조
console.log(me.name); // Lee
// 프로토타입 메서드 호출
me. sayHi(); // Hi! My name is Lee
// 정적 메서드 호출
Person.sayHello(); // Hello!
```

<div><img src= "/assets/img/post/class_vs_constfunc.PNG"></div>

- 클래스와 생성자 함수의 정의 방식은 형태적인 면에서 매우 유사하다.

#### 25.4 인스턴스 생성

- **클래스는 생성자 함수이며 new 연산자와 함께 호출되어 인스턴스를 생성**한다.

```js
class Person {}
// 인스턴스 생성
const me = new Person();
console.log(me); // Person {}
```

- 함수는 new 연산자의 사용 여부에 따라 일반 함수로 호출되거나 인스턴스 생성을 위한 생성자 함수로 호출되지만 **클래스는 인스턴스를 생성하는 것이 유일한 존재 이유이므로 반드시 new 연산자와 함께 호출**해야 한다.

#### 25.5 메서드

- 클래스 몸체에는 0개 이상의 메서드만 선언할 수 있다. 클래스 몸체에서 정의할 수 있는 메서드는 constructor(생성자), 프로토타입 메서드, 정적 메서드의 세 가지가 있다.

##### 25.5.1 constructor

- **constructor는 인스턴스를 생성하고 초기화하기 위한 특수한 메서드**다. constructor는 이름을 변경할 수없다.

<div><img src= "/assets/img/post/class_function.PNG"></div>

- 이처럼 클래스는 평가되어 함수 객체가 된다. 18.2절 “함수 객체의 프로퍼티”에서 살펴보았듯이 클래스도 함수 객체 고유의 프로퍼티를 모두 갖고 있다. 함수와 동일하게 프로토타입과 연결되어 있으며 자신의 스코프체인을 구성한다.
- **모든 함수 객체가 가지고 있는 prototype 프로퍼티가 가리키는 프로토타입 객체의 constructor 프로퍼티는 클래스 자신을 가리키고 있다.** 이는 클래스가 인스턴스를 생성하는 생성자 함수라는 것을 의미한다. 즉, new 연산자와 함께 클래스를 호출하면 클래스는 인스턴스를 생성한다.
- 생성자 함수와 마찬가지로 constructor 내부에서 this에 추가한 프로퍼티는 인스턴스 프로퍼티가 된다. constructor 내부의 this는 생성자 함수와 마찬가지로 클래스가 생성한 인스턴스를 가리킨다.
- 흥미로운 것은 클래스가 평가되어 생성된 함수 객체나 클래스가 생성한 인스턴스 어디에도 constructor 메서드가 보이지 않는다는 것이다. 이는 **클래스 몸체에 정의한 constructor가 단순한 메서드가 아니라는 것을 의미**한다.
- constructor는 메서드로 해석되는 것이 아니라 클래스가 평가되어 생성한 함수 객체 코드의 일부가 된다. 다시 말해, 클래스 정의가 평가되면 constructor의 기술된 동작을 하는 함수 객체가 생성된다.
###### 클래스의 constructor 메서드와 프로토타입의 constructor 프로퍼티
- 클래스의 constructor 메서드와 프로토타입의 constructor 프로퍼티는 이름이 같아 혼동하기 쉽지만 직접적인 관련이 없다. 프로토타입의 constructor 프로퍼티는 모든 프로토타입이 가지고 있는 프로퍼티이며, 생성자 함수를 가리킨다.
- constructor는 생성자 함수와 유사하지만 몇 가지 차이가 있다.
    - constructor는 클래스 내에 최대 한 개만 존재할 수 있다.
    - constructor는 생략할 수 있다.
    - 프로퍼티가 추가되어 초기화된 인스턴스를 생성하려면 constructor 내부에서 this에 인스턴스 프로퍼티를 추가한다.
    - 인스턴스를 생성할 때 클래스 외부에서 인스턴스 프로퍼티의 초기값을 전달하려면 constructor에 매개변수를 선언하고 인스턴스를 생성할 때 초기값을 전달한다.
    - constructor는 별도의 반환문을 갖지 않아야 한다. 
- 이처럼 constructor 내에서는 인스턴스의 생성과 동시에 인스턴스 프로퍼티 추가를 통해 인스턴스의 초기화를 실행한다. 따라서 **인스턴스를 초기화하려면 constructor를 생략해서는 안된다.**

##### 25.5.2 프로토타입 메서드

- 생성자 함수를 사용하여 인스턴스를 생성하는 경우 프로토타입 메서드를 생성하기 위해서는 다음과 같이 명시적으로 프로토타입에 메서드를 추가해야 한다.

```js
// 생성자 함수
function Person(name) {
    this.name = name;
}
// 프로토타입 메서드
Person.prototype.sayHi = function () {
    console.log( Hi! My name is ${this.name} );
}；
const me = new Person('Lee');
me.sayHi(); // Hi! My name is Lee

```

- 클래스 몸체에서 정의한 메서드는 생성자 함수에 의한 객체 생성 방식과는 다르게 클래스의 prototype 프로퍼티에 메서드를 추가하지 않아도 기본적으로 프로토투타입 메서드가 된다.
- 생성자 함수와 마찬가지로 클래스가 생성한 인스턴스는 프로토타입 체인의 일원이 된다.

```js
// me 객체의 프로토타입은 Person.prototype이다.
Object.getPrototypeOf(me) === Person.prototype; // — true
me instanceof Person; // — true
// Person.prototype의 프로토타입은 Object.prototype이다.
Object.getPrototypeOf(Person.prototype) === Object.prototype; // — true
me instanceof Object; // — true
// me 객체의 constructor는 Person 클래스다.
me.constructor == Person; // — true
```

- 이처럼 클래스 몸체에서 정의한 메서드는 인스턴스의 프로토타입에 존재하는 프로토타입 메서드가 된다. 인스턴스는 프로토타입 메서드를 상속받아 사용할 수 있다.
- 프로토타입 체인은 기존의 모든 객체 생성 방식(객체 리터럴, 생성자 함수, Object.create 메서드 등)뿐만 아니라 클래스에 의해 생성된 인스턴스에도 동일하게 적용된다. 생성자 함수의 역할을 클래스가 할 뿐이다.
- **결국 클래스는 생성자 함수와 같이 인스턴스를 생성하는 생성자 함수라고 볼 수 있다. 다시 말해, 클래스는 생성자 함수와 마찬가지로 프로토타입 기반의 객체 생성 메커니즘이다.**

##### 25.5.3 정적 메서드

- 19.12절 “정적 프로퍼티/메서드”에서 살펴보았듯이 정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있는 메서드를 말한다.
- 클래스에서는 메서드에 static 키워드를 붙이면 정적 메서드(클래스 메서드)가 된다.

```js
class Person {
    // 생성자
    constructor(name) {
        // 인스턴스 생성 및 초기화
        this.name = name;
    }
    // 정적 메서드
    static sayHi() {
        console.log('Hi!')；
    }
}
```

- 정적 메서드는 클래스에 바인딩된 메서드가 된다. 클래스는 함수 객체로 평가되므로 자신의 프로퍼티/메서드를 소유할 수 있다. **클래스는 클래스 정의(클래스 선언문이나 클래스 표현식)가 평가되는 시점에 함수 객체가 되므로 인스턴스와 달리 별다른 생성 과정이 필요 없다.** 따라서 정적 메서드는 클래스 정의 이후 인스턴스를 생성하지 않아도 호출할 수 있다.

```js
// 정적 메서드는 클래스로 호출한다.
// 정적 메서드는 인스턴스 없이도 호출할 수 있다.
Person.sayHi(); // Hi!
```

##### 25.5.4 정적 메서드와 프로토타입 메서드의 차이

- 정적 메서드와 프로토타입 메서드의 차이는 다음과 같다.
    - 정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다르다.
    - 정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.
    - 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.
- 22.2절 “함수 호출 방식과 this 바인딩”에서 살펴보았듯이 **메서드 내부의 this는 메서드를 소유한 객체가 아니라 메서드를 호출한 객체, 즉 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체에 바인딩**된다.
- **프로토타입 메서드는 인스턴스로 호출해야 하므로 프로토타입 메서드 내부의 this는 프로토타입 메서드를 호출한 인스턴스를 가리킨다.** 
- **정적 메서드는 클래스로 호출해야 하므로 정적 메서드 내부의 this는 인스턴스가 아닌 클래스를 가리킨다.** 즉, 프로토타입 메서드와 정적 메서드 내부의 this 바인딩이 다르다.
- 따라서 **메서드 내부에서 인스턴스 프로퍼티를 참조할 필요가 있다면 this를 사용해야 하며, 이러한 경우 프로토타입 메서드로 정의해야 한다.** 하지만 메서드 내부에서 인스턴스 프로퍼티를 참조해야 할 필요가 없다면 this를 사용하지 않게 된다.
- 물론 메서드 내부에서 this를 사용하지 않더라도 프로토타입 메서드로 정의할 수 있다. 하지만 반드시 인스턴스를 생성한 다음 인스턴스로 호출해야 하므로 **this를 사용하지 않는 메서드는 정적 메서드로 정의하는 것이 좋다.**
- 표준 빌트인 객체인 Math, Number, JSON, Object, Reflect 등은 다양한 정적 메서드를 가지고 있다. 이들 정적 메서드는 애플리케이션 전역에서 사용할 유틸리티 함수다.

```js
// 표준 빌트인 객체의 정적 메서드
Math.max(1, 2, 3); // — 3
Number.isNaN(NaN)； // — true
JSON.stringify({ a: 1 }); // — "{"a"：l}"
Object.is({}, {}); // — false
Reflect.has({ a： 1 }, 'a')； // — true
```

- 이처럼 클래스 또는 생성자 함수를 하나의 네임스페이스로 사용하여 정적 메서드를 모아 놓으면 이름 충돌 가능성을 줄여 주고 관련 함수들을 구조화할 수 있는 효과가 있다. 이 같은 이유로 **정적 메서드는 애플리케이션 전역에서 사용할 유틸리티 함수를 전역 함수로 정의하지 않고 메서드로 구조화할 때 유용**하다.

#### 25.6 클래스의 인스턴스 생성 과정

- new 연산자와 함께 클래스를 호출하면 생성자 함수와 마찬가지로 클래스의 내부 메서드 [[Construct]]가 호출된다. 클래스는 new 연산자 없이 호출할 수 없다.\
1) 인스턴스 생성과 this 바인딩
- **new 연산자와 함께 클래스를 호출**하면 constructor의 내부 코드가 실행되기에 앞서 **암묵적으로 빈 객체가 생성**된다. 이 빈 객체가 바로 (아직 완성되지는 않았지만) 클래스가 생성한 인스턴스다. 이때 클래스가 생성한 **인스턴스의 프로토타입으로 클래스의 prototype 프로퍼티가 가리키는 객체가 설정**된다. 그리고 암묵적으로 생성된 빈 객체, 즉 **인스턴스는 this에 바인딩**된다. 따라서 constructor 내부의 this는 클래스가 생성한 인스턴스를 가리킨다.
2) 인스턴스 초기화
- **constructor의 내부 코드가 실행**되어 this에 바인딩되어 있는 인스턴스를 초기화한다.\
3) 인스턴스 반환
- 클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 **this가 암묵적으로 반환**된다.

```js
class Person {
// 생성자
    constructor(name) {
        // 1. 암묵적으로 인스턴스가 생성되고 thisotl 바인딩된다.
        console.log(this); // Person {}
        console.log(Object.getPrototypeOf(this) === Person.prototype); // true
        // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
        this.name = name;
        // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
    }
}
```

#### 25.7 프로퍼티

##### 25.7.1 인스턴스 프로퍼티

- 인스턴스 프로퍼티는 constructor 내부에서 정의해야 한다.

```js
class Person {
    constructor(name) {
        // 인스턴스 프로퍼티
        this.name = name;
    }
}
const me = new Person( 'Lee');
console.log(me); // Person {name: "Lee"}
```

- 25.6절 “클래스의 인스턴스 생성 과정”에서 살펴보았듯이 **constructor 내부 코드가 실행되기 이전에 constructor 내부의 this에는 이미 클래스가 암묵적으로 생성한 인스턴스인 빈 객체가 바인딩**되어 있다.
- 생성자 함수에서 생성자 함수가 생성할 인스턴스의 프로퍼티를 정의하는 것과 마찬가지로 constructor 내부에서 this에 인스턴스 프로퍼티를 추가한다.
- ES6의 클래스는 다른 객체지향 언어처럼 private, public, protected 키워드와 같은 접근 제한자를 지원하지 않는다. 따라서 인스턴스 프로퍼티는 언제나 public하다. 다행히도 private한 프로퍼티를 정의할 수 있는 사양이 현재 제안 중에 있다. 

##### 25.7.2 접근자 프로퍼티

- 16.3.2 절 “접근자 프로퍼티”에서 살펴보았듯이 접근자 프로퍼티는 자체적으로는 값([[Value]] 내부 슬롯)을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다.

```js
class Person {
    constructor(firstName, lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
    // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
    // getter 함수
    get fullName() {
     return '${this.firstName} ${this.lastName}';
    }
    // setter 함수
    set fullName(name) {
      [this.firstName, this.lastName] = name.split(' ');
    }
}
const me = new Person('Ungmo', 'Lee' );
// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log('${me.firstName} ${me.lastName}'); // Ungmo Lee
// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
me.fullName = ’Heegun Lee1;
console.log(me); // {firstName: "Heegun", lastName: "Lee"}
// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName어/ 접근하면 getter 함수가 호출된다.
console.log(me.fullName); // Heegun Lee
// fullName은 접근자 프로퍼티다.
11 접근자 프로퍼티는 get, set, enumerable, configurable 프로퍼티 어트리뷰트를 갖는다.
console.log(Object.getOwnPropertyDescriptor(Person.prototype, 'fullName'));
// {get: f, set: f, enumerable: false, configurable: true}
```

- 접근자 프로퍼티는 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수, 즉 getter 함수와 setter 함수로 구성되어 있다.
- 클래스의 메서드는 기본적으로 프로토타입 메서드가 된다. 따라서 클래스의 접근자 프로퍼티 또한 인스턴스 프로퍼티가 아닌 프로토타입의 프로퍼티가 된다.

<div><img src= "/assets/img/post/class_access_property.PNG"></div>

##### 25.7.3 클래스 필드 정의 제안

- 먼저 클래스 필드가 무엇인지 살펴보자. **클래스 필드(필드 또는 멤버)는 클래스 기반 객체지향 언어에서 클래스가 생성할 인스턴스의 프로퍼티를 가리키는 용어**다. 클래스 기반 객체지향 언어인 자바의 클래스 정의를 살펴보자.
- 자바스크립트의 클래스 몸체에는 메서드만 선언할 수 있다. 따라서 클래스 몸체에 자바와 유사하게클래스 필드를 선언하면 문법 에러가 발생한다. 하지만 위 예제를 최신 브라우저(Chrome 72 이상) 또는 최신 Node.js(버전 12 이상)에서 실행하면 문법 에러(SyntaxError)가 발생하지 않고 정상 동작한다. 그 이유를 살펴보자.
- 자바스크립트에서도 인스턴스 프로퍼티를 마치 클래스 기반 객체지향 언어의 클래스 필드처럼 정의할수 있는 새로운 표준 사양인 “Class field declarations”가 2021년 1월 현재, TC39 프로세스11의 stage3(candidate)에 제안되어 있다.
- 클래스 몸체에서 클래스 필드를 정의할 수 있는 클래스 필드 정의 제안은 아직 ECMAScript의 정식 표준 사양으로 승급되지 않았다. 하지만 최신 브라우저(Chrome 72 이상)와 최신 Node.js(버전 12 이상)는 표준 사양으로 승급이 확실시되는 이 제안을 선제적으로 미리 구현해 놓았다. 따라서 최신 브라우저와 최신 Node.js에서는 클래스 필드를 클래스 몸체에 정의할 수 있다.
- 클래스 몸체에서 클래스 필드를 정의하는 경우 this에 클래스 필드를 바인딩해서는 안 된다. this는 클래스의 constructor와 메서드 내에서만 유효하다.

```js
class Person {
    // this에 클래스 필드를 바인딩해서는 안 된다.
    this.name =''; // SyntaxError: Unexpected token '
}
```

- 클래스 필드를 참조하는 경우 자바와 같은 클래스 기반 객체지향 언어에서는 this를 생략할 수 있으나 자바스크립트에서는 this를 반드시 사용해야 한다.

```js
class Person {
    // 클래스 필드
    name = 'Lee';
    constructor() {
        console.log(name); // ReferenceError: name is not defined
    }
}
new Person();
```

- 클래스 필드에 초기값을 할당하지 않으면 undefined를 갖는다.
- 인스턴스를 생성할 때 외부의 초기값으로 클래스 필드를 초기화해야 할 필요가 있다면 constructor에서 클래스 필드를 초기화해야 한다.
- **인스턴스를 생성할 때 클래스 필드를 초기화할 필요가 있다면 constructor 밖에서 클래스 필드를 정의할 필요가 없다.** 클래스 필드를 초기화할 필요가 있다면 어차피 constructor 내부에서 클래스 필드를 참조하여 초기값을 할당해야 한다. 이때 this, 즉 클래스가 생성한 인스턴스에 클래스 필드에 해당하는 프로퍼티가 없다면 자동 추가되기 때문이다.
- **함수는 일급 객체이므로 함수를 클래스 필드에 할당할 수 있다.** 따라서 클래스 필드를 통해 메서드를 정의할 수도 있다.

```js
class Person {
    // 클래스 필드에 문자열을 할당
    name = 'Lee';
    // 클래스 필드에 함수를 할당
    getName = function () {
        return this.name;
    }
    // 화살표 함수로 정의할 수도 있다.
    // getName = () => this.name;
}
const me = new Person();
console.log(me); // Person {name: "Lee", getName: f}
console.log(me.getName()); // Lee
- 
```

- 이처럼 **클래스 필드에 함수를 할당하는 경우, 이 함수는 프로토타입 메서드가 아닌 인스턴스 메서드가 된다.** 모든 클래스 필드는 인스턴스 프로퍼티가 되기 때문이다. 따라서 클래스 필드에 함수를 할당하는 것은 권장하지 않는다.

##### 25.7.4 private 필드 정의 제안

- 24.5 절 “캡슐화와 정보 은닉”에서 살펴보았듯이 **자바스크립트는 캡슐화를 완전하게 지원하지 않는다. ES6의 클래스도 생성자 함수와 마찬가지로 다른 클래스 기반 객체지향 언어에서는 지원하는 private, public, protected 키워드와 같은 접근 제한자를 지원하지 않는다.** 따라서 인스턴스 프로퍼 티는 인스턴스를 통해 클래스 외부에서 언제나 참조할 수 있다. 즉, 언제나 public이다.
- 클래스 필드 정의 제안을 사용하더라도 클래스 필드는 기본적으로 public하기 때문에 외부에 그대로 노출
된다.
- 다행히도 2021 년 1 월 현재, TC39 프로세스의 stage 3(candidate)에는 private 필드를 정의할 수 있는 새로운 표준 사양이 제안되어 있다. 표준 사양으로 승급이 확실시되는 이 제안도 최신 브라우저(Chrome 74 이상)와 최신 Node.js(버전 12 이상)에 이미 구현되어 있다.
- 다음 예제를 살펴보자. private 필드의 선두에는 #을 붙여준다. private 필드를 참조할 때도 #을 붙어주어야한다.

```js
class Person {
    // private 필드 정의
    #name = '';
    constructor(name) {
    // private 필드 참조
    this.#name = name;
    }
}
const me = new Person('Lee');
// private 필드 #name은 클래스 외부에서 잠조할 수 없다.
console.log(me.#name);
// SyntaxError: Private field 'tfname' must be declared in an enclosing class
```

###### 타입스크립트
- C#의 창시자인 덴마크 출신 소프트웨어 엔지니어 아네르스 하일스베르가 개발을 주도한 자바스크립트의 상위 확장 superset인 **타입스크립트(Typescript)는 클래스 기반 객체지향 언어가 지원하는 접근 제한자인 public, private, protected를 모두 지원하며. 의미 또한 기본적으로 동일**하다.

<div><img src= "/assets/img/post/public_private.PNG"></div>

- 이처럼 클래스 외부에서 private 필드에 직접 접근할 수 있는 방법은 없다. 다만 접근자 프로퍼티를 통해간접적으로 접근하는 방법은 유효하다.

```js
class Person {
    // private 필드 정의
    #name = ' ' :
    constructor(name) {
        this.#name = name;
    }
    // name은 접근자 프로퍼티다.
    get name() {
        // private 필드를 참조하여 trim한 다음 반환한다.
        return this.#name.trim();
    }
}
const me = new Person(' Lee 1);
console.log(me.name); // Lee
```

- private 필드는 반드시 클래스 몸체에 정의해야 한다. private 필드를 직접 constructor에 정의하면 에러가 발생한다.

##### 25.7.5 static 필드 정의 제안

- 25.5.3절 “정적 메서드”에서 살펴보았듯이 클래스에는 static 키워드를 사용하여 정적 메서드를 정의할 수 있다. 하지만 static 키워드를 사용하여 정적 필드를 정의할 수는 없었다. 하지만 static public 필드, static private 필드, static private 메서드를 정의할 수 있는 새로운 표준 사양인 “Static class features”가 2021년 1월 현재, TC39 프로세스의 stage 3 candidate）에 제안되어 있다. 이 제안 중에서 static public/private 필드는 2021년 1월 현재, 최신 브라우저（Chrome 72 이상）와 최신 Node.js（버전12 이상）에 이미 구현되어 있다.

#### 25.8 상속에 의한 클래스 확장

##### 25.8.1 클래스 상속과 생성자 함수 상속

- **상속에 의한 클래스 확장은 지금까지 살펴본 프로토타입 기반 상속과는 다른 개념**이다. 프로토타입 기반 상속은 프로토타입 체인을 통해 다른 객체의 자산을 상속받는 개념이지만 상속에 의한 클래스 확장은 기존 클래스를 상속받아 새로운 클래스를 확장하여 정의하는 것이다.
- 클래스와 생성자 함수는 인스턴스를 생성할 수 있는 함수라는 점에서 매우 유사하다. 하지만 **클래스는 상속을 통해 기존 클래스를 확장할 수 있는 문법이 기본적으로 제공되지만 생성자 함수는 그렇지 않다.**
- **클래스는 상속을 통해 다른 클래스를 확장할 수 있는 문법인 extends 키워드가 기본적으로 제공**된다. extends 키워드를 사용한 클래스 확장은 간편하고 직관적이다. 하지만 **생성자 함수는 클래스와 같이 상속을 통해 다른 생성자 함수를 확장할 수 있는 문법이 제공되지 않는다.**

##### 25.8.2 extends 키워드

- 상속을 통해 클래스를 확장하려면 extends 키워드를 사용하여 상속받을 클래스를 정의한다.

```js
// 수퍼（베아스/부모）클래스
class Base {}
// 서브（파생/자식）클래스
class Derived extends Base {}
```

- extends 키워드의 역할은 수퍼클래스와 서브클래스 간의 상속 관계를 설정하는 것이다. 클래스도 프로토타입을 통해 상속 관계를 구현한다.

<div><img src= "/assets/img/post/extends_keyword.PNG"></div>

- 수퍼클래스와 서브클래스는 인스턴스의 프로토타입 체인뿐 아니라 클래스 간의 프로토타입 체인도 생성한다. 이를 통해 프로토타입 메서드, 정적 메서드 모두 상속이 가능하다.

##### 25.8.3 동적 상속

- extends 키워드는 클래스뿐만 아니라 생성자 함수를 상속받아 클래스를 확장할 수도 있다. 단, extends 키워드 앞에는 반드시 클래스가 와야 한다.

```js
// 생성자 함수
function Base(a) {
    this.a = a;
}
// 생성자 함수를 상속받는 서브클래스
class Derived extends Base {}
const derived = new Derived(l);
console.log(derived); // Derived {a: 1}
```

##### 25.8.4 서브 클래스의 constructor

- 25.5.1 절 "constructor"에서 살펴보았듯이 클래스에서 constructor를 생략하면 클래스에 다음과 같이 비어있는 constructor가 암묵적으로 정의된다.

```js
constructor() {}
```

- 서브클래스에서 constructor를 생략하면 클래스에 다음과 같은 constructor가 암묵적으로 정의된다. args는 new 연산자와 함께 클래스를 호출할 때 전달한 인수의 리스트다.

```js
constructor( ... args) { super( ... args); }
```

- super()는 수퍼클래스의 constructor(super-constructor)를 호출하여 인스턴스를 생성한다.
-  수퍼클래스와 서브클래스 모두 constructor를 생략하면 빈 객체가 생성된다. 프로퍼티를 소유하는 인스턴스를 생성하려면 constructor 내부에서 인스턴스에 프로퍼티를 추가해야 한다.

##### 25.8.5 super 키워드

- super 키워드는 함수처럼 호출할 수도 있고 this와 같이 식별자처럼 참조할 수 있는 특수한 키워드다. super는 다음과 같이 동작한다.
###### super 호출
    - super를 호출하면 수퍼클래스의 constructor(super—constructor)를 호출한다.
    - super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.
- super를 호출할 때 주의할 사항은 다음과 같다.
    - 서브클래스에서 constructor를 생략하지 않는 경우 서브클래스의 constructor에서는 반드시 super를 호출해야한다.
    - 서브클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없다.
    - super는 반드시 서브클래스의 constructor에서만 호출한다. 서브클래스가 아닌 클래스의 constructor나 함수에서 super를 호출하면 에러가 발생한다.

##### 25.8.6 상속 클래스의 인스턴스 생성 과정

- 상속 관계에 있는 두 클래스가 어떻게 협력하며 인스턴스를 생성하는지 살펴보도록 하자. 이를 통해 super를 더욱 명확하게 이해할 수 있을 것이다.
- 25.6절 “클래스의 인스턴스 생성 과정”에서 살펴본 클래스가 단독으로 인스턴스를 생성하는 과정보다 상속 관계에 있는 두 클래스가 협 력하며 인스턴스를 생성하는 과정은 좀 더 복잡하다.
- 직사각형을 추상화한 Rectangle 클래스와 상속을 통해 Rectangle 클래스를 확장한 ColorRectangle 클래스 를 정의해 보자.

```js
// 수퍼클래스
class Rectangle {
    constructor(width, height) {
        this.width = width;
        this.height = height;
    }
    getArea() {
        return this.width * this.height;
    }
    toString() {
     return width = ${this.width}, height = ${this.height} ;
    }
}
// 서브클래스
class ColorRectangle extends Rectangle {
    constructor(width, height, color) {
        super(width, height);
        this.color = color;
    }
    // 머/서드 오버라이딩
    toString() {
        return super.toString() + color = ${this.color} ;
    }
}
const ColorRectangle = new ColorRectangle(2, 4, ' red');
console.log(ColorRectangle); // ColorRectangle {width： 2, height： 4, color： "red"}
// 상속을 통해 getArea 메서드를 호출
console.log(ColorRectangle.getArea()); // 8
// 오버라이딩된 toString 메서드를 호출
console. log( ColorRectangle. toString()); // width = 2, height = 4, color = red
```

1) 서브클래스의 super 호출
- 자바스크립트 엔진은 클래스를 평가할 때 수퍼클래스와 서브클래스를 구분하기 위해 "base" 또는 "derived"를 값으로 갖는 내부 슬롯 [ [ConstructorKind] ]를 갖는다. 이를 통해 수퍼클래스와 서브클래스
는 new 연산자와 함께 호출되었을 때의 동작이 구분된다.
- 다른 클래스를 상속받지 않는 클래스（그리고 생성자 함수）는 25.6절 “클래스의 인스턴스 생성 과정”에서 살펴보았듯이 new 연산자와 함께 호출되었을 때 암묵적으로 빈 객체, 즉 인스턴스를 생성하고 이를 this에 바
인딩한다.
- **하지만 서브클래스는 자신이 직접 인스턴스를 생성하지 않고 수퍼클래스에게 인스턴스 생성을 위임**한다. 이것이 바로 서브클래스의 constructor에서 반드시 super를 호출해야 하는 이유다.


2) 수퍼클래스의 인스턴스 생성과 this 바인딩
- 수퍼클래스의 constructor 내부의 코드가 실행되기 이전에 암묵적으로 빈 객체를 생성한다. 이 빈 객체가 바로 (아직 완성되지는 않았지만) 클래스가 생성한 인스턴스다. 그리고 암묵적으로 생성된 빈 객체, 즉 인스턴스는 this에 바인딩된다. 따라서 수퍼클래스의 constructor 내부의 this는 생성된 인스턴스를 가리킨다.
```js
// 수퍼클래스
class Rectangle {
constructor(width, height) {
    // 암묵적으로 빈 객체, 즉 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // ColorRectangle {}
    // new 연산자와 함께 호출된 함수, 즉 new.target은 ColorRectangle이다.
    console.log(new.target); // ColorRectangle
    ...
```

- 이때 인스턴스는 수퍼클래스가 생성한 것이다. 하지만 **new 연산자와 함께 호출된 클래스가 서브클래스라는 것이 중요**하다. 즉, new 연산자와 함께 호출된 함수를 가리키는 new.target은 서브클래스를 가리킨다. 따라서 인스턴스는 new.target이 가리키는 서브클래스가 생성한 것으로 처리된다.

3) 수퍼클래스의 인스턴스 초기화
- **수퍼클래스의 constructor가 실행되어 this에 바인딩되어 있는 인스턴스를 초기화**한다. 즉. this에 바인딩 되어 있는 인스턴스에 프로퍼 티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

4) 서브클래스 constructor로의 복귀와 this 바인딩
- super의 호출이 종료되고 제어 흐름이 서브클래스 constructor로 돌아온다. **이때 super가 반환한 인스턴스가 this에 바인딩된다. 서브클래스는 별도의 인스턴스를 생성하지 않고 super가 반환한 인스턴스를 this에 바인딩하여 그대로 사용**한다.

```js
// 서브클래스
class ColorRectangle extends Rectangle {
    constructor(width, height, color) {
    super(width, height);
    // super가 반환한 인스턴스가 this에 바인딩된다.
    console.log(this); // ColorRectangle {width: 2, height: 4}
    ...
```

- **이처럼 super가 호출되지 않으면 인스턴스가 생성되지 않으며, this 바인딩도 할 수 없다. 서브클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없는 이유가 바로 이 때문**이다. 따라서 서브클래스 constructor 내부의 인스턴스 초기화는 반드시 super 호출 이후에 처리되어야 한다.

5) 서브클래스의 인스턴스 초기화

- super 호출 이후, 서브클래스의 constructor에 기술되어 있는 인스턴스 초기화가 실행된다. 즉, this에 바인딩되어 있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

6) 인스턴스 반환

- 클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.

<h3>끝!</h3>
