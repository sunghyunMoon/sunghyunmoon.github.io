---
title: 3장 고급 타입
categories:
- 우아한 타입스크립트 with 리액트
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/elegant_typescript.png"
---

### 3.1 타입스크립트만의 독자적 타입 시스템

- 타입스크립트는 자바스크립트 자료형에서 제시되지 않은 독자적인 타입 시스템을 가지고 있다. 하지만 엄밀히 말하면 타입스크립트의 타입 시스템이 내포하고 있는 개념은 모두 자바스크립트에서 기인한 것이다. 단지 자바스크립트로 표현할 수단과 필요성이 없었을 뿐이다. 자바스크립트의 슈퍼셋으로 정적 타이핑을 할 수 있는 타입스크립트가 등장하면서 비로소 타입스크립트의 타입 시스템이 구축되었다.
- 이 장에서 소개하는 모든 타입 시스템은 타입스크립트에만 존재하는 키워드지만, 그 개념은 자바스크립트에 기인한 타입 시스템이라는 점을 인지하고 각 타입을 살펴 보자.

#### 3.1.1 any 타입

- any 타입은 앞서 말한 대로 자바스크립트에 존재하는 모든 값을 오류 없이 받을 수 있다. 즉, 자바스크립트에서의 기본적인 사용 방식과 같으므로 타입을 명시하지 않은 것과 동일한 효과를 나타낸다.
- 자연스레 any 타입의 효용성에 대해 의문을 가질 수 있다. 앞의 예시에서 볼 수 있듯이 any 타입은 타입스크립트로 달성하고자 하는 정적 타이핑을 무색하게 만들 수 있다. **타입스크립트는 동적 타이핑 특징을 가진 자바스크립트에 정적 타이핑을 적용하는 것이 주된 목적이지만 any 타입은 이러한 목적을 무시하고 자바스크립트의 동적 타이핑으로 돌아가는 것과 비슷한 결과를 가져온다.**
- 따라서 any 타입을 변수에 할당하는 것은 지양해야 할 패턴으로 알려져 있다. 다시 말해 any
를 회피하는 것은 좋은 습관으로 간주된다. 하지만 타입스크립트에서 any 타입을 어쩔 수 없이 사용해야 할 때가 있는데 대표적으로 3가지 사례를 들 수 있다.
    - 개발 단계에서 임시로 값을 지정해야 할 때
        - 매우 복잡한 구성 요소로 이루어진 개발 과정에서 추후 값이 변경될 가능성이 있거나 아직 세부 항목에 대한 타입이 확정되지 않은 경우가 생길 수 있다. 이럴 때 해당 값을 any로 지정하면 경고 없이 개발을 계속할 수 있다. 즉 타입을 세세하게 명시하는 데 소요되는 시간을 절약할 수있다. 하지만 any 타입으로 지정하고 나서 다른 타입으로 바꾸는 과정이 누락되면 문제가 발생할 수 있으므로 주의해야 한다.
    - 어떤 값을 받아올지 또는 넘겨줄지 정할 수 없을 때
        - 타입스크립트의 타입을 사용하면 정적 타이핑의 효과를 얻을 수 있다. 그러나 자바스크립트 입장에서는 어떤 값의 타입을 명확하게 지정하기 어려운 상황이 발생할 수 있다. 예를 들어 API 요청 및 응답 처리, 콜백 함수 전달, 타입이 잘 정제되지 않아 파악이 힘든 외부 라이브러리 등을 사용할 때는 어떤 인자를 주고받을지 특정하기 힘들다. 이처럼 주고받을 값이 명확하지 않을 때 열린 타입 （any 타입）을 선언해야 할 수 있다.
    - 값을 예측할 수 없을 때 암묵적으로 사용
        - 외부 라이브러리나 웹 API의 요청에 따라 다양한 값을 반환하는 API가 존재할 수 있다. 대표적인 예로 브라우저의 Fetch API> 들 수 있다. Fetch API의 일부 메서드는 요청 이후의 응답을 특정 포맷으로 파싱하는데 이때 반환 타입이 any로 매핑되어 있는 것 을 볼 수 있다.
- any 타입은 개발자에게 편의성과 확장성을 제공하기도 하지만 해당 값을 컨트롤하려면 파악해야 할 정보도 많다. 즉 도구의 도움을 받을 수 없는 상태에서 온전히 개발자 스스로 책임을 져야함을 의미한다. 이런 맥락을 이해하지 못하면 협업 시 실수할 가능성이 커진다.

#### 3.1.2 unknown 타입

- unknown 타입은 any 타입과 유사하게 모든 타입의 값이 할당될 수 있다. 그러나 any를 제외한 다른 타입으로 선언된 변수에는 unknown 타입 값을 할당할 수 없다. 아래 표는 any와 unknown 타입의 비교 내용을 담고 있다.

<div><img src= "/assets/img/post/anyvsunknown.PNG"></div>

```js

let unknownvalue： unknown;
unknownValue = 100; // any 타입과 유사하게 숫자이든
unknownvalue = "hello world"; // 문자열이든
unknownvalue = () =〉console.log("this is any type"); // 함수이든 상관없이 할당이 가능하지만
let someValuel： any = unknownvalue; // (0) any 타입으로 선언된 변수를 제외한 다른 변수는
모두 할당이 불가
let someValue2： number = unknownvalue; // (X)
let someValue3： string = unknownvalue; // (X)
```

- unknown타입은 타입스크립트 3.0이 릴리스 될 때 추가되었는데 기존 타입 시스템에서 부족한 부분을 보완하기 위해 등장했다. unknown에 대응되는 자바스크립트 자료형이 무엇인지 쉽게 떠오르지 않을 만큼 타입스크립트만의 타입 시스템이라고 볼 수 있는데, unknown 타입은 이름처럼 무엇이 할당될지 아직 모르는 상태의 타입을 말한다. 이렇게만 보면 any 타입과 비슷한데 왜 unknown 타입이 추가되었을까?
- 앞선 예시를 다시 보자. 재미있는 점은 분명 함수를 unknown 타입 변수에 할당할 때는 컴파일러가 아무런 경고를 주지 않지만 이를 실행하면 다음과 같은 에러가 발생한다는 것이다.

```js
// 할당하는 시점에서는 에러가 발생하지 않음
const unknownFunction： unknown = () => console.log("this is unknown type");
// 하지만 실행 시에는 에러가 발생; Error： Object is of type 'unknown’.ts (2571)
unknownFunction();
```

- 비단 함수뿐만 아니라 객체의 속성 접근, 클래스 생성자 호출을 통한 인스턴스 생성 등 객체 내부에 접근하는 모든 시도에서 에러가 발생한다. unknown 타입은 어떤 타입이 할당되었는지 알 수 없음을 나타내기 때문에 unkonwn 타입으로 선언된 변수는 값을 가져오거나 내부 속성에 접근할 수 없다. 이는 unknown 타입으로 할당된 변수는 어떤 값이든 올 수 있음을 의미하는 동시에 개발자에게 엄격한 타입 검사를 강제하는 의도를 담고 있다.
- any 타입을 사용하면 어떤 값이든 허용된다. 앞서 어떤 값이 할당될지 파악하기 어려운 상황에서 any 타입을 지정하여 임시로 문제를 회피하는 예시도 살펴보았다. 그리고 나중에 **any 타입을 특정 타입으로 수정해야 하는 것을 깜빡하고 누락하면 어떤 값이든 전달될 수 있기 때문에 런타임에 예상치 못한 버그가 발생할 가능성이 높아진다는 것도 설명했다.**
- **unknown 타입은 이러한 상황을 보완하기 위해 등장한 타입이다. any 타입과 유사하지만 타입 검사를 강제하고 타입이 식별된 후에 사용할 수 있기 때문에 any 타입보다 더 안전하다.** 따라서 데이터 구조를 파악하기 힘들 때 any 타입 대신 unknown 타입으로 대체해서 사용하는 방법이 권장된다.
- unknown은 어떨 때 사용할 수 있을까요?
    - 강제 타입 캐스팅을 통해 타입을 전환할 때 사용합니다. const env = process.env unknown as ProcessEnv 같은 식으로요.
    - 예상할 수 없는 데이터라면 unknown을 씁니다. 타입스크립트 4.4부터 try— catch 에러의 타입이 any에서 unknown으로 변경되어서 에러 핸들링할 때도 unknown을 사용합니다. 한편 as unknown as Type같이 강제 타입 캐스팅을 하기도 하는데 사실 이것도 any와 다를 바 없어서 지양해야합니다.

#### 3.1.3 void 타입

- 다른 정적 타입 언어에서 void라는 타입을 이미 접해본 적이 있다면 이해하기 쉬울 것이다. 타입스크립트에서 함수가 어떤 값을 반환하지 않는 경우에는 void를 지정하여 사용한다고 생각하면 된다.

#### 3.1.4 never 타입

- never 타입도 일반적으로 함수와 관련하여 많이 사용되는 타입이다. never라는 단어가 내포하고 있는 의미처럼 never 타입은 값을 반환할 수 없는 타입을 말한다. 여기서 값을 반환하지 않는 것과 반환할 수 없는 것을 명확히 구분해야 한다. 자바스크립트에서 값을 반환할 수 없는 예는 크게 2가지로 나눌 수 있다.

    - 에러를 던지는 경우
        - throw 키워드를 사용하면 에러를 발생시킬 수 있는데, 이는 값을 반환하는 것으로 간주하지 않는다. 따라서 특정 함수가 실행 중 마지막에 에러를 던지는 작업을 수행한다면 해당 함수의 반환 타입은 never이다.

```js
function generateError(res： Response)： never {
    throw new Error(res.getMessageO);
}
```

    - 무한히 함수가 실행되는 경우
        - 드물지만 함수 내에서 무한 루프를 실행하는 경우가 있을 수 있다. 무한 루프는 결국 함수가 종료되지 않음을 의미하기 때문에 값을 반환하지 못한다

#### 3.1.5 Array 타입

- 이미 자바스크립트에서도 확인할 수 있는 자료형 인데도 왜 타입스크립트에서 다시 배열을 다루는지 의문이 들 수 있을 것이다. 타입스크립트에서 다시 Array를 언급하는 이유를 다음과 같이 제시할 수 있다.
    - 엄밀히 말하면 자바스크립트에서는 배열을 객체에 속하는 타입으로 분류한다. 즉, **자바스크립트에서는 배열을 단독으로 배열이라는 지료형에 국한하지 않는다.**
    - 타입스크립트에서 Array라는 타입을 사용하기 위해서는 타입스크립트의 특수한 문법을 함께 다뤄야 핸다.
- 앞서 설명한 대로. 자바스크립트의 배열은 동적 언어의 특징에 따라 어떤 값이든 배열의 원소로 허용한다. 즉, 하나의 배열로 선언된 변수에 숫자, 문자열, 객체 등 자료형이 무엇이든 상관없이 원소를 삽입하고 관리할 수 있다.
-  타입스크립트에서는 일반적으로 배열의 크기까지 제한하지는 않지만 정적 타입의 특성을 살려 명시적인 타입을 선언하여 해당 타입의 원소를 관리하는 것을 강제한다.
- 2가지 방식으로 배열 타입을 선언할 수 있는데 두 방식 간의 차이점은 선언하는 형식 외에는 없다. 개인의 선호나 팀의 컨벤션에 따라 하나의 방식으로 통일하거나 2가지 방식을 혼용해서 사용해도 큰 문제는 없다.
- 기본적으로 자바스크립트의 동작은 배열 원소의 타입을 구분하지 않기 때문에 다양한 자료형의 원소를 함께 다룰 수 있는데, 만약 숫자형과 문자열 등 여러 타입을 모두 관리해야 하는 배열을 선언하려면 유니온 타입을 사용할 수 있다.

```js
const arrayl： Array<number | string> = [1, "string"];
const array2: number[] | string[] = [1, "string"];
// 후자의 방식은 아래와 같이 선언할 수도 있다
const arrays： (number | string)[] = [1, "string"];
```

- 앞서 언급한 대로 타입스크립트에서 배열 타입을 명시하는 것만으로 배열의 길이까지는 제한할 수 없다. 그러나 **튜플은 배열 타입의 하위 타입으로 기존 타입스크립트의 배열 기능에 길이 제한까지 추가한 타입 시스템이라고 볼 수 있다.**
- 튜플은 타입스크립트의 타입 시스템과 대괄호를 사용해서 선언할 수 있다. 대괄호 안에 타입시스템을 기술하는 것이 배열 타입과 유일하게 다른 점이다. 이때 대괄호 안에 선언하는 타입의 개수가 튜플이 가질 수 있는 원소의 개수를 나타낸다. 즉, 튜플은 배열의 특정 인덱스에 정해진 타입을 선언하는 것과 같다.

```js
let tuple： [number] = [1]；
tuple = [1, 2]; // 불가능
tuple = [1, "string"]; // 불가능
let tuple： [number, string, boolean] = [1, "string", true]; // 여러 타입과 혼합도 가능하다
```

- 기본적으로 타입스크립트에서의 배열과 튜플은 자바스크립트와 달리 제한적으로 쓰인다. **배열은 사전에 허용하지 않은 타입이 서로 섞이는 것을 방지하여 타입 안정성을 제공**한다. **튜플은 길이까지 제한하여 원소 개수와 타입을 보장**한다.
- 이처럼 **타입을 제한하는 것은 자바스크립트가 갖는 동적 언어의 자유로움으로 인해 발생할 수 있는 런타임 에러와 유지보수의 어려움을 막기 위한 것이다.** 특히 **튜플의 경우 컨벤션을 잘 지키고 각 배열 원소의 명확한 의미와 쓰임을 보장할 때 더욱 안전하게 사용할 수 있는 타입**이다.
- 튜플의 유용한 쓰임새를 알아보기 위해 사용자 인터페이스를 만들기 위한 자바스크립트 라이브러리인 리액트 예시를 살펴보자. 리액트는 16.8 버전부터 도입된 훅이라는 요소 중 UseState는 튜플 타입을 반환한다. 첫 번째 원소는 훅으로부터 생성 및 관리되는 상태 값을 의미하고, 두 번째 원소는 해당 상태를 조작할 수 있는 세터(setter)를 의미한다.

#### 3.1.6 enum 타입

- enum 타입은 열거형이라고도 부르는데 타입스크립트에서 지원하는 특수한 타입이다. enum이라는 키워드는 다른 언어에서도 사용하는 개념이기에 익숙할 수 있을 것이다. enum은 일종의 구조체를 만드는 타입 시스템이다. 
- 기본적인 추론 방식은 숫자 0부터 1씩 늘려가며 값을 할당하는 것이다. 또한 각 멤버에 명시적으로 값을 할당할 수 있다. 모든 멤버에 일일이 값을 할당할 수도 있지만, 일부 멤버에 값을 직접 할당하지 않아도 타입스크립트는 누락된 멤버를 아래와 같은 방식으로 이전 멤버 값의 숫자를 기준으로 1씩 늘려가며 자동으로 할당한다.

```js
enum ProgrammingLanguage {
Typescript = "Typescript",
Javascript = "Javascript",
Java = 300,
Python = 400,
Kotlin, // 401
Rust, // 402
Go, // 403
}
PrograinniingLangi』age[2]； // "Java"
```

- enum 타입은 주로 문자열 상수를 생성하는 데 사용된다. 이를 통해 응집력 있는 집합 구조체를 만들 수 있으며, 사용자 입장에서도 간편하게 활용할 수 있다. 또한 열거형은 그 자체로 변수 타입으로 지정할 수 있다. 이때 열거형을 타입으로 가지는 변수는 해당 열거형이 가지는 모든 멤버를 값으로 받을 수 있다. 이런 특성은 코드의 가독성을 높여준다.
- 다만 열거형에 사용할 때는 주의해야 할 점이 있다. 먼저 숫자로만 이루어져 있거나 타입스크립트가 자동으로 추론한 열거형은 안전하지 않은 결과를 낳을 수 있다. 맨 처음 예시를 보면 **역방향으로도 접근할 수 있음을 보여준다. 여기서 할당된 값을 넘어서는 범위로 역방향으로 접근하더 라도 타입스크립트는 막지 않는다.**
- 이러한동작을 막기 위해 const enum으로 열거형을 선언하는 방법이 있다. 이 방식은 역방향으로의 접근을 허용하지 않기 때문에 자바스크립트에서의 객체에 접근하는 것과 유사한 동작을 보장한다.

```js
ProgrammingLanguage[200]; // undefined를 출력하지만 별다른 에러를 발생시키지 않는다
// 다음과 같이 선언하면 위와 같은 문제를 방지할 수 있다
const enum ProgrammingLanguage {
    // ...
}
```

- 그러나 const enum으로 열거형을 선언하더라도 숫자 상수로 관리되는 열거형은 선언한 값 이외의 값을 할당하거나 접근할 때 이를 방지하지 못한다. 반면 문자열 상수 방식으로 선언한 열거형은 미리 선언하지 않은 멤버로 접근을 방지한다. 따라서 문자열 상수 방식으로 열거형을 사용하는 것이 숫자 상수 방식보다 더 안전하며 의도하지 않은 값의 할당이나 접근을 방지하는 데 도움이 된다.

```js
const enum NUMBER {
    ONE = 1,
    TWO = 2,
}
const myNumber: NUMBER = 100; // NUMBER enum에서 100을 관리하고 있지 않지만 이는 에러를 발생시키지 않는다
const enum STRING_NUMBER {
ONE = "ONE",
TWO = "TWO",
}
const myStringNumber： STRING_NUMBER = "THREE"; // Error
```

### 3.2 타입 조합

- 이 절에서는 앞에서 다룬 개념을 응용하거나 약간의 내용을 덧붙여 좀 더 심화한 타입 검사를 수행하는 데 필요한 지식을 살펴본다.

#### 3.1.1 교차 타입(Intersection)

- 교차 타입을 사용하면 여러 가지 타입을 결합하여 하나의 단일 타입으로 만들 수 있다. 다시 말해 기존에 존재하는 다른 타입들을 합쳐서 해당 타입의 모든 멤버를 가지는 새로운 타입을 생성하는 것이다. 교차 타입은 &을 사용해서 표기한다. 결과물로 탄생한 단일 타입에는 타입 별칭(type alias)을 붙일 수도 있다.
- 아래처럼 ProductItemWithDiscount 타입의 변수를 선언하고 값을 할당하면 Productltem의 모든 멤버와 discountAmount까지 멤버로 가지게 된다.

```js
type Productltem = {
    id： number;
    name: string;
    type: string;
    price： number；
    imageUrl： string；
    quantity： number;
}；

type ProductItemWithDiscount = Productitem & { discountAmount: number };
```

#### 3.1.2 유니온 타입(Union)

- 교차 타입 (A & B)이 타입 A와 타입 B를 모두 만족하는 경우라면, 유니온 타입은 타입 A 또는 타입 B 중 하나가 될 수 있는 타입을 말하며 A | B 같이 표기한다. 주로 특정 변수가 가질수 있는 타입을 전부 나열하는 용도로 사용된다. 
- 교차 타입과 마찬가지로 2개 이상의 타입을 이어 붙일 수 있고 타입 별칭을 통해 중복을 줄일 수도 있다. 아래 예시는 Productitem 혹은 Carditem이 될 수 있는 유니온 타입인 PromotionEventltem을 나타낸다. 즉, 이벤트 프로모션의 대상으로 상품이 될 수도 있고 카드가 될 수도 있다는 의미이다.

```js
type Cardltem = {
    id： number;
    name： string；
    type: string;
    imageUrl： string;
}；
type PromotionEventltem = Productltem | Cardltem;

const printPromotionltem = (item： PromotionEventltem) => {
    console.log(itein.name); // 0
    console.log(iteni.quantity); // 컴파일 에러 발생
};
```

#### 3.1.3 인덱스 시그니처(Index Signatures)

- 인덱스 시그니처는 특정 타입의 속성 이름은 알 수 없지만 속성값의 타입을 알고 있을 때 사용하는 문법이다. 인터페이스 내부에 [Key: K]: T 꼴로 타입을 명시해주면 되는데 이는 해당 타입의 속성 키는 모두 K 타입이어야 하고 속성값은 모두 T 타입을 가져야 한다는 의미다.

```js
interface IndexSignatureEx {
[key： string]： number;
}
```

- 인덱스 시그니처를 선언할 때 다른 속성을 추가로 명시해줄 수 있는데 이때 추가로 명시된 속성은 인덱스 시그니처에 포함되는 타입이어야 한다.

```js

interface IndexSignatureEx2 {
    [key： string]： number | boolean;
    length： number;
    isValid： boolean;
    name： string; // 에러 발생
}
```

#### 3.1.4 인덱스드 액세스 타입(Indexed Access Types)

- 인덱스드 엑세스 타입은 다른 타입의 **특정 속성이 가지는 타입을 조회하기 위해 사용**된다. 아래 첫 번째 예시 (IndexedAccess)는 Example 타입의 a 속성이 가지는 타입을 조회하기 위한 인덱스드 엑세스 타입이다. 인덱스에 사용되는 타입 또한 그 자체로 타입이기 때문에 유니온 타입, keyof, 타입 별칭 등의 표현을 사용할수 있다.

```js
type Example = {
    a： number；
    b： string;
    c： boolean；
};
type IndexedAccess = Example["a"];
type IndexedAccess2 = Example["a" | "b"]; // number j string
type IndexedAccessB = Example[keyof Example]; // number | string | boolean
type ExAlias = "b" | "c";
type IndexedAccess4 = Example[ExAlias]; // string ! boolean
```

#### 3.1.5 맵드 타입(Mapped Types)

- 보통 map이라고 하면 유사한 형태를 가진 여러 항목의 목록 A를 변환된 항목의 목록 브로 바꾸는 것을 의미한다. 이와 마찬가지로 **맵드 타입은 다른 타입을 기반으로 한 타입을 선언할 때 사용하는 문법인데, 인덱스 시그니처 문법을 사용해서 반복적인 타입 선언을 효과적으로줄일 수 있다.**

```js
type Example = {
    a： number;
    b: string;
    c： boolean;
};

type Subset<T> = {
[K in keyof T]?: T[K];
};
// keyof Example = a | b | c
// T[K] : 프로퍼티 'K'의 타입을 'T'에서 가져옴

const aExample： Subset<Example> = { a: 3 };
const bExampl은: Subset<Example> = { b： "hello" };
const acExample: Subset<Example> = { a： 4, c： true };
```

- 맵드 타입이 실제로 사용된 예시를 살펴보자. 배달의민족 선물하기 서비스에는 ‘바텀시트’ 라는 컴포넌트가 존재한다. 밑에서부터 스르륵 올라오는 모달이라고 생각하면 되는데 이 바텀시트는 선물하기 서비스의 최근 연락처 목록, 카드 선택, 상품 선택 등 여러 지면에서 사용되고 있다. 바텀시트마다 각각 resolver, isOpened 등의 상태를 관리하는 스토어가 필요한데 이 스토어의 타입 (BottomSheetStore)을 선언해줘야 한다.
- 이때 BottomSheetMap에 존재하는 모든 키에 대해 일일이 스토어를 만들어줄 수도 있지만 불필요한 반복이 발생하게 된다. 이럴 때는 인덱스 시그니처 문법을 사용해서 BottomSheetMap을 기반으로 각 키에 해당하는 스토어를 선언할수 있다. 이처럼 반복 작업을 효율적으로 처리할 수있다.

```js
const BottomSheetMap = {
    RECENT.CONTACTS: RecentContactsBottomSheet,
    CARD.SELECT: CardSelectBottomSheet,
    SORT_FILTER： SortFilterBottomSheet,
    PRODUCT.SELECT: ProductSelectBottomSheet,
    REPLY_CARD_SELECT: ReplyCardSelectBottomSheet,
    RESEND： ResendBottomSheet,
    STICKER： StickerBottomSheet,
    BASE: null,
};

export type B0TT0M_SHEET_ID = keyof typeof BottomSheetMap;
// "RECENT.CONTACTS" | "CARD.SELECT" | "SORT_FILTER" | "PRODUCT.SELECT" | "REPLY_CARD_SELECT" | "RESEND" | "STICKER" | "BASE";

// 불필요한 반복이 발생한다
type BottomSheetStore = {
RECENT.CONTACTS: {
    resolver?: (payload： any) => void;
    args?： any;
    isOpened： boolean;
};
CARD.SELECT: {
    resolver?: (payload： any) => void;
    args?; any;
    isOpened： boolean;
}；
SORT.FILTER: {
    resolver?： (payload: any) => void;
    args?： any;
    isOpened: boolean;
}；
// ...
}；

// Mapped Types를 통해 효율적으로 타입을 선언할 수 있다
type Bottomsheetstore = {
    [index in BOTTOM_SHEET_ID]: {
        resolver?： (payload： any) => void;
        args?: any;
        isOpened： boolean；
    }；
}；
```

- 덧붙여 맵드 타입에서는 as 키워드를 사용하여 키를 재지정할 수 있다. 앞서 봤던 바텀시트를 다시 살펴보자. BottomSheetStore의 키 이름에 BottomSheetMap의 키 이름을 그대로 쓰고 싶은 경우가 있을 수 있고, 모든 키에 _BOTTOM_SHEET를 붙이는 식으로 공통된 처리를 적용하여 새로운 키를 지정하고 싶을 수도 있다. 이럴 때는 아래 예시처럼 as 키워드를 사용해서 효율적으로 처리할 수 있다.

```js
type BottomSheetStore = {
    [index in BOTTOM_SHEET_ID as '${index}_BOTTOM_SHEET']: {
        resolver?: (payload： any) => void;
        args?： any;
        isOpened： boolean;
    }；
}；
```

#### 3.1.6 템플릿 리터럴 타입(Template Literal Types)

- 템플릿 리터럴 타입은 자바스크립트의 템플릿 리터럴 문자열을 사용하여 문자열 리터럴 타입을 선언할 수 있는 문법이다.

```js
type Stage =
| "init"
| "select-image"
| "edit-image"
| "decorateord"
| "capture-image";
type StageName = `${Stage}-stage`;
// 'init-stage' i 'select-image-stage' ! ’edit-image-stage' i 'decorate-card-stage'i
’capture-image-stage'
```

#### 3.1.7 제네릭(Generic)

- 제네릭Generic은 C나 자바 같은 정적 언어에서 다양한 타입 간에 재사용성을 높이기 위해 사용하는 문법이다. 타입스크립트도 정적 타입을 가지는 언어이기 때문에 제네릭 문법을 지원하고 있다.
- .좀 더 자세히 타입스크립트 제네릭의 개념을 풀어보면 **함수, 타입, 클래스 등에서 내부적으로 사용할 타입을 미리 정해두지 않고 타입 변수를 사용해서 해당 위치를 비워 둔 다음에, 실제로 그 값을 사용할 때 외부에서 타입 변수 자리에 타입을 지정하여 사용하는 방식을 말한다.**
- 이렇게 하면 함수, 타입, 클래스 등 여러 타입에 대해 하나하나 따로 정의하지 않아도 되기 때문에 재사용성이 크게 향상된다.

```js
type ExampleArrayType<T> = T[];
const arrayl： ExampleArrayType<string> = ["치킨", "피자", "우동"];
```

- 앞서 제네릭이 일반화된 데이터 타입을 말한다고 했는데, 이 표현만 보면 any의 쓰임과 혼동할 수도 있을 것이다. 하지만 둘은 명확히 다르다. 둘의 차이는 배열을 떠올리면 쉽게 알 수 있다.
- any 타입의 배열에서는 배열 요소들의 타입이 전부 같지 않을 수 있다. 쉽게 말해 타입 정보를 잃어버린다고 생각하면 편하다. 즉, any를 사용하면 타입 검사를 하지 않고 모든 타입이 허용되는 타입으로 취급된다. 반면에 제네릭은 any처럼 아무 타입이나 무분별하게 받는 게 아니라, 배열 생성 시점에 원하는 타입으로 특정할 수 있다. 다시 말해 제네릭을 사용하면 배열 요소가 전부 동일한 타입이 라고 보장할 수 있다.
- 또한 특정 요소 타입을 알 수 없을 때는 제네릭 타입에 기본값을 추가할 수 있다.

```js
interface SubmitEventxT = HTMLElement> extends SyntheticEvent<T> { submitter： T;

}
```

- 다시 언급하지만 제네릭은 일반화된 데이터 타입을 의미한다고 했다. 따라서 함수나 클래스 등의 내부에서 제네릭을 사용할 때 어떤 타입이든 될 수 있다는 개념을 알고 있어야 한다. 
- 특정한 타입에서만 존재하는 멤버를 참조하려고 하면 안된다. 예를 들어 배열에만 존재하는 length 속성을 제네릭에서 참조하려고 하면 당연히 에러가 발생한다. 컴파일러는 어떤 타입이 제네릭에 전달될지 알 수 없기 때문에 모든 타입이 length 속성을 사용할 수는 없다고 알려주는 것이다.

```js
function exampleFunc2<T>(arg： T)： number {
return arg.length; // 에러 발생: Property 'length' does not exist on type 'T'
}
```

- 이럴 때는 제네릭 꺾쇠괄호 내부에 "length 속성을 가진 타입만 받는다"라는 제약을 걸어줌으로써 length 속성을 사용할 수 있게끔 만들 수 있다.

```js
interface TypeWithLength {
    length： number;
}
function exampleFunc2<T extends TypeWithLength>(arg: T): number {
    return arg.length;
}
```

- 제네릭을 사용할 때 주의해야 할 점이 있다. **파일 확장자가 tsx일 때 화살표 함수에 제네릭을 사용하면 에러가 발생한다.** tsx는 타입스크립트 + JSX이므로 제네릭의 꺾쇠괄호와 태그의 꺾쇠괄호를 혼동하여 문제가 생기는 것이다. JSX에서는 태그를 나타내는 데 꺾쇠괄호(<>)를 사용한다.
- **이러한 상황을 피하기 위해서는 제네릭 부분에 extends 키워드를 사용하여 컴파일러에게 특정 타입의 하위 타입만 올 수 있음을 확실히 알려주면 된다.** 보통 제네릭을 사용할 때는 function 키워드로 선언하는 경우가 많다.

```js
// 에러 발생: JSX element 'T' has no corresponding closing tag
const arrowExampleFunc = <T></>(arg：T): T[] => {
    return new Array(3).fill.(arg);
};

// 에러 발생 X
const arrowExampleFunc2 = <T extends {}>(arg： T)： T[] => {
    return new Array(3).fiVL(arg);
}
```

<h3>끝!</h3>
