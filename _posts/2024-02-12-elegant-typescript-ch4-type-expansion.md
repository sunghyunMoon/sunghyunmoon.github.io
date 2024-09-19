---
title: 4장 타입 확장하기 / 좁히기
categories:
- 우아한 타입스크립트 with 리액트
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/elegant_typescript.png"
---

### 4.1 타입 확장하기

- 타입 확장은 기존 타입을 사용해서 새로운 타입을 정의하는 것을 말한다. 기본적으로 타입스크립트에서는 interface와 type 키워드를 사용해서 타입을 정의하고 extends, 교차 타입, 유니온 타입을 사용하여 타입을 확장한다. 타입 확장의 장점과 더불어 extends, 교차 타입, 유니온 타입 간의 차이를 파악하고 언제 사용하면 좋을지 살펴보자.

#### 4.1.1 타입 확장의 장점

- 타입 확장의 가장 큰 장점은 코드 중복을 줄일 수 있다는 것이다. 타입스크립트 코드를 작성하다 보면 필연적으로 중복되는 타입 선언이 생기기 마련이다. 이때 중복되는 타입을 반복적으로 선언하는 것보다 기존에 작성한 타입을 바탕으로 타입 확장을 함으로써 불필요한
 코드 중복을 줄일 수 있다. 아래 예시를 보자.

```js
/**
* 메뉴 요소 타입
* 메뉴 이룜, 이미지, 할인율, 재고 정보를 담고 있다
* */
interface BaseMenuItem {
    itemName: string | null;
    itemlmageUrl: string | null;
    itemDiscountAmount: number;
    stock： number | null;
}
/**
* 장바구니 요소 타입
* 메뉴 타입에 수량 정보가 추가되었다
* */
interface BaseCartltem extends BaseMenuItem {
    quantity： number；
}
```

- 이처럼 **타입 확장은 중복된 코드를 줄일 수 있게 해준다.** 그뿐만 아니라 BaseCartltem이 BaseMenuItem에서 확장되었다는 것을 쉽게 확인할 수 있는 것처럼 더 명시적인 코드를 작성할 수 있게 된다. interface 키워드 대신 typ은을 쓴다면 아래와 같이 코드를 작성하면 된다.

```js
type BaseMenuItem = {
    itemName： string | null;
    itemlmageUrl： string | null;
    itemDiscountAmount: number;
    stock： number | null;
}；
type BaseCartltem = {
    quantity： number；
} & BaseMenuItem;
```

- **타입 확장은 중복 제거, 명시적인 코드 작성 외에도 확장성이란 장점을 가지고 있다.** 앞에서 정의한 BaseCartltem을 활용하면 요구 사항이 늘어날 때마다 새로운 Cartitem 타입을 확장하여 정의할 수 있다. 아래 예시를 살펴보자.

```js
/**
* 수정할 수 있는 장바구니 요소 타입
* 품절 여부, 수정할 수 있는 옵션 배열 정보가 추가되었다
* */
interface EditableCartItem extends BaseCartltem {
    isSoldOut: boolean;
    optionGroups; SelectableOptionGroup[];
}

/**
* 이벤트 장바구니 요소 타입
* 주문 가능 여부에 대한 정보가 추가되었다
* */
interface EventCartltem extends BaseCartltem {
    orderable： boolean;
}
```

- 더욱이, 기존 장바구니 요소에 대한 요구 사항이 변경되어도 BaseCartltem 타입만 수정흐!고 EditableCartItem이나 EventCartltem은 수정하지 않아도 되기 때문에 효율적이다.

#### 4.1.2 유니온 타입

- 유니온 타입은 2개 이상의 타입을 조합하여 사용하는 방법이다. 집합 관점으로 보면 유니온 타입을 합집합으로 해석할 수 있다.

```js
type MyUnion = A | B;
```

- 여기서 주의해야 할 게 있다. 유니온 타입으로 선언된 값은 유니온 타입에 포함된 모든 타입이 공통으로 갖고 있는 속성에만 접근할 수 있다.

```js
interface CookingStep {
    orderld: string;
    price： number;
}
interface DeliveryStep {
    orderld: string;
    time: number;
    distance: string;
}
function getDeliveryDistance(step： CookingStep | DeliveryStep) {
    return step.distance;
    // Property 'distance' does not exist on type 'CookingStep | DeliveryStep’
    // Property 'distance' does not exist on type 'CookingStep'
}
```

- distance는 Deliverystep에만 존재하는 속성이다. 인자로 받는 step의 타입이 CookingStep이라면 distance 속성을 찾을 수 없기 때문에 에러가 발생한다.
- 즉, step이라는 유니온 타입은 CookingStep 또는 Deliverystep 타입에 해당할 뿐이지 CookingStep이면서 DeliveryStep인 것은 아니다.

#### 4.1.3 교차 타입

- 교차 타입도 기존 타입을 합쳐 필요한 모든 기능을 가진 하나의 타입을 만드는 것으로 이해할 수 있다.

```js
/* 배달 팁 */
interface DeliveryTip {
    tip： string；
}
/* 별점 */
interface StarRating {
    rate: number;
}
/* 주문 필터 */
type Filter = DeliveryTip & StarRating;

const filter： Filter = {
    tip： "1000원 이하",
    rate： 4,
}
```

- 교차 타입을 사용할 때 타입이 서로 호환되지 않는 경우도 있다. 다음 코드를 살펴보자.

```js
type IdType = string | number;
type Numeric = number | boolean;

type Universal = IdType & Numeric;
```

- Universal은 IdType과 Numeric의 교차 타입이므로 두 타입을 모두 만족하는 경우에만 유지된다. 따라서 Universal의 타입은 number가 된다.

#### 4.1.4 extends와 교차 타입

- 유니온 타입과 교차 타입을 사용한 새로운 타입은 오직 type 키워드로만 선언할 수 있다.
- 주의할 점은 extends 키워드를 사용한 타입이 교차 타입과 100% 상응하지는 않는다는 것이다. 예시를 보자.

```js
interface DeliveryTip {
    tip: number;
}
interface Filter extends DeliveryTip {
    tip：string;
    // Interface 'Filter' incorrectly extends interface 'DeliveryTip'
    // Types of property 'tip' are incompatible
    // Type 'string' is not assignable to type 'number'
}
```

- DeliveryTip을 extends로 확장한 Filter 타입에 string 타입의 속성 tip을 선언하면 tip의 타입이 호환되지 않는다는 에러가 발생한다.

```js
type DeliveryTip = {
    tip: number;
}；
type Filter = DeliveryTip & {
    tip： string;
};
```

- extends를 &로 바꿨을 뿐인데 에러가 발생하지 않는다. 이때 tip 속성의 타입은 number일까? string일까? 정답은 never다.
- type 키워드는 교차 타입으로 선언되었을 때 새롭게 추가되는 속성에 대해 미리 알 수 없기
때문에 선언 시 에러가 발생하지 않는다. 하지만 tip이라는 같은 속성에 대해 서로 호환되지
않는 타입이 선언되어 결국 never 타입이 된 것이다.

### 4.2 타입 좁히기 - 타입 가드

- 타입스크립트에서 타입 좁히기는 변수 또는 표현식의 타입 범위를 더 작은 범위로 좁혀나가는 과정을 말한다. 타입 좁히기를 통해 더 정확하고 명시적인 타입 추론을 할 수 있게 되고, 복잡한 타입을 작은 범위로 축소하여 타입 안정성을 높일 수 있다.

#### 4.2.1 타입 가드에 따라 분기 처리하기

- **타입스크립트에서의 분기 처리는 조건문과 타입 가드를 활용하여 변수나 표현식의 타입 범위를 좁혀 다양한 상황에 따라 다른 동작을 수행하는 것을 말한다. 타입 가드는 런타임에 조건문을 사용하여 타입을 검사하고 타입 범위를 좁혀주는 기능을 말한다.** 구체적인 상황을 보면서 이해해보자.
- 타입스크립트로 개발하다 보면 여러 타입을 할당할 수 있는 스코프에서 특정 타입을 조건으로 만들어 분기 처리하고 싶을 때가 있다. 여러 타입을 할당할 수 있다는 것은 변수가 유니온 타입 또는 any 타입 등 여러 가지 타입을 받을 수 있다는 것을 말하는데 조건으로 검사하려는 타입보다 넓은 범위를 갖고 있다.
- 예를 들어 어떤 함수가 A | B 타입의 매개변수를 받는다고 가정해보자. 인자 타입이 A 또는 B 일 때를 구분해서 로직을 처리하고 싶다면 어떻게 해야 할까?
- **if문을 사용해서 처리하면 될 것 같지만 컴파일 시 타입 정보는 모두 제거되어 런타임에 존재하지 않기 때문에 타입을 사용하여 조건을 만들 수는 없다. 즉, 컴파일해도 타입 정보가 사라지지 않는 방법을 사용해야 한다.**
- 특정 문맥 안에서 타입스크립트가 해당 변수를 타입 A로 추론하도록 유도하면서 런타임에서 도 유효한 방법이 필요한데, 이때 타입 가드를 사용하면 된다. 타입 가드는 크게 자바스크립트 연산자를 사용한 타입 가드와 사용자 정의 타입 가드로 구분할 수 있다.
- **자바스크립트 연산자를 활용한 타입 가드는 typeof, instanceof, in과 같은 연산자를 사용해서 제어문으로 특정 타입 값을 가질 수밖에 없는 상황을 유도하여 자연스럽게 타입을 좁히는 방식이다.** 자바스크립트 연산자를 사용하는 이유는 런타임에 유효한 타입 가드를 만들기 위해서다. 런타임에 유효하다는 말은 타입스크립트뿐만 아니라 자바스크립트에서도 사용할 수 있는 문법이어야 한다는 의미이다.
- 사용자 정의 타입 가드는 사용자가 직접 어떤 타입으로 값을 좁힐지를 직접 지정하는 방식이다. 그럼 어떤 상황에서 타입 가드를 활용할 수 있을지 살펴보자.

#### 4.2.2 원시 타입을 추론할 때: typeof 연산자 활용하기

- **typeof 연산자를 활용하면 원시 타입에 대해 추론할 수 있다. 다만 typeof는 자바스크립트 타입 시스템만 대응할 수 있다.** 자바스크립트의 동작 방식으로 인해 null과 배열 타입 등이 object 타입으로 판별되는 등 복잡한 타입을 검증하기에는 한계가 있다. 따라서 typeof 연산자는 주로 원시 타입을 좁히는 용도로만 사용할 것을 권장한다.

#### 4.2.3 인스턴스화된 객체 타입을 판별할 때: instanceof 연산자 활용하기

- 다음 예시는 selected 매개변수가 Date인지를 검사한 후에 Range 타입의 객체를 반환할 수 있도록 분기 처리하고 있다.

```js
interface Range {
start： Date;
end： Date;
}

interface DatePickerProps {
selectedDates?: Date | Range;
}
const DatePicker = ({ selectedDates }： DatePickerProps) => {
    const [selected, setSelected] = useState(convertToRange(selectedDates));
    //...
}；
export function convertToRange(selected?： Date | Range)： Range | undefined {
    return selected instanceof Date
    ? { start： selected, end： selected }
    : selected;
}
```

- typeof 연산자를 주로 원시 타입을 판별하는 데 사용한다면, instanceof 연산자는 인스턴스화된 객체 타입을 판별하는 타입 가드로 사용할 수 있다.

#### 4.2.4 객체의 속성이 있는지 없는지에 따른 구분: in 연산자 활용하기

- in 연산자는 객체에 속성이 있는지 확인한 다음에 true 또는 false를 반환한다. in 연산자를 사용하면 속성이 있는지 없는지에 따라 객체 타입을 구분할 수 있다.
- 자바스크립트의 in 연산자는 런타임의 값만을 검사하지만 타입스크립트에서는 객체 타입에
속성이 존재하는지를 검사한다. 여러 객체 타입을 유니온 타입으로 가지고 있을 때 in 연산자를 사용해서 속성의 유무에 따라 조건 분기를 할 수 있다.

#### 4.2.5 is 연산자로 사용자 정의 타입 가드 만들어 활용하기

- 직접 타입 가드 함수를 만들 수도 있다. 이러한 방식의 타입 가드는 반환 타입이 타입 명제인 함수를 정의하여 사용할 수 있다. 타입 명제는 A is B 형식으로 작성하면 되는데 여기서 A는 매개변수 이름이고 B는 타입이다. 참/거짓의 진릿값을 반환하면서 반환 타입을 타입 명제로 지정하게 되면 반환 값이 참일 때 A 매개변수의 타입을 B 타입으로 취급하게 된다. 아래 예시를살펴보자.

```js
const isDestinationCode = (x： string)： x is Destinationcode =>
    destinationCodeList.includes(x);

```

- isDestinationCode는 string 타입의 매개변수가 destinationCodeList 배열의 원소 중 하나인지를 검사하여 boolean을 반환하는 함수이다. **함수의 반환 값을 boolean이 아닌 x is Destinationcode로 타이핑하여 타입스크립트에게 이 함수가 사용되는 곳의 타입을 추론할 때 해당 조건을 타입 가드로 사용하도록 알려준다.**
- 만약 isDestinationCode의 반환 값 타이핑을 x is Destinationcode가 아닌 boolean으로 했다면 타입스크립트는 어떻게 추론할까? ? 개발자는 if문 내부에서 str 타입이 Destinationcode라는 것을 알 수 있다. Array. includes를 해석할수 있기 때문이다.
- 하지만 타입스크립트는 isDestinationCode 함수 내부에 있는 includes 함수를 해석해 타입 추론을 할 수 없다. 타입스크립트는 if문 스코프의 str 타입을 좁히지 못하고 string으로만 추론한다. destinationNames의 타입은 DestinationName[]이기 때문에 string 타입의 str을 push할 수 없다는 에러가 발생한다. 이처럼 타입스크립트에게 반환 값에 대한 타입정보를 알려주고 싶을 때 is를 사용할 수 있다. 반환 값의 타입을 X is Destinationcode로 알려줌으로써 타입스크립트는 if문 스코프의 str 타입을 Destinationcode로 추론할 수 있게 된다.

### 4.3 타입 좁히기 - 식별할 수 있는 유니온(Discriminated Unions)

- 종종 태그되 유니온 으로도 불리는 식별할 수 있는 유니온은 타입 좁히기에 널리 사용되는 방식이다. 이 절에서는 예시를 살펴보면서 식별할 수 있는 유니온을 어떨 때 사용할 수 있는지와 장점을 알아본다.

### 4.3.1 에러 정의하기

- 배달의민족의 선물하기 서비스는 선물을 보낼 때 필요한 값을 사용자가 올바르게 입력했는지 를 확인하는 유효성 검사를 진행한다. 유효성 에러가 발생하면 사용자에게 다양한 방식으로 에러를 보여 주는데 우아한형제들에서는 이 에러를 크게 텍스트 에러, 토스트 에러, 얼럿 에러
로 분류한다. 이들 모두 유효성 에러로 에러 코드(errorcode)와 에러 메시지(errorMessage)를 가지고 있으며, 에러 노출 방식에 따라 추가로 필요한 정보가 있을 수 있다. 예를 들어 토스트 에러는 토스트를 얼마 동안 띄울 것인지에 대한 정보가 필요하다.

```js
type TextError = {
    errorCode： string;
    errorMessage: string;
}；
type ToastError = {
    errorCode: string;
    errorMessage： string;
    toastShowDuration： number; // 토스트를 띄워줄 시간
}；
type AlertError = {
    errorCode; string;
    errorMessage： string;
    onConfirm: () => void; // 얼럿 창의 확인 버튼을 누른 뒤 액션
};
```

- 이 에러 타입의 유니온 타입을 원소로 하는 배열을 정의해보면 다음과 같을 것이다.

```js
type ErrorFeedbackType = TextError | ToastError | AlertError;
const errorArr： ErrorFeedbackType[] = [
{ errorcode: "100", errorMessage： "텍스트 에러"},
{ errorCode： "200", errorMessage： "토스트 에러", toastShowDuration： 3000 },
{ errorcode： "300", errorMessage： "얼럿 에러", onConfirm： () => {} },
]；
```

- TextError, ToastError, AlertError의 유니온 타입인 ErrorFeedbackType의 원소를 갖는 배열 errorArr를 정의함으로써 다양한 에러 객체를 관리할 수 있게 되었다. 여기서 해당 배열에 에러 타입별로 정의한 필드를 가지는 에러 객체가 포함되길 원한다고 해보자. 즉, ToastError의 toastShowDuration 필드와 AlertError의 onConfirm 필드를 모두 가지는 객체에 대해서는 타입 에러를 뱉어야 할 것이다.

```js

const errorArr： ErrorFeedbackType[] = [
    // ...
    {
    errorCode： "999",
    errorMessage： "잘못된 에러",
    toastShowDuration： 3000,
    onConfirm： () => {},
    }, // expected error
]；
```

- 하지만 이 코드를 작성했을 때 자바스크립트는 덕 타이핑 언어이기 때문에 별도의 타입 에러를 뱉지 않는 것을 확인할 수 있다. 이런 상황에서 타입 에러가 발생하지 않는다면 앞으로의 개발 과정에서 의미를 알 수 없는 무수한 에러 객체가 생겨날 위험성이 커진다.

### 4.3.2 식별할 수 있는 유니온

- 따라서 에러 타입을 구분할 방법이 필요하다. **각 타입이 비슷한 구조를 가지지만 서로 호환되지 않도록 만들어주기 위해서는 타입들이 서로 포함 관계를 가지지 않도록 정의**해야 한다. 이때 적용할 수 있는 방식이 바로 식별할 수 있는 유니온을 활용하는 것이다. **식별할 수 있는 유니온이란 타입 간의 구조 호환을 막기 위해 타입마다 구분할 수 있는 판별자를 달아주어 포함 관계를 제거하는 것이다.**
- 판별자의 개념으로 errorType이라는 필드를 새로 정의해보자. 각 에러 타입마다 이 필드에 대해 다른 값을 가지도록 하여 판별자를 달아주면 이들은 포함 관계를 벗어나게 된다.

```js
type TextError = {
    errorType： "TEXT";
    errorCode： string;
    errorMessage： string;
}；
type ToastError = {
    errorType： "TOAST";
    errorCode： string;
    errorMessage: string;
    toastShowDuration: number;
}；
type AlertError = {
    errorType： "ALERT";
    errorCode： string;
    errorMessage： string;
    onConfirm： () => void;
}；
```

- 에러 객체에 대한 타입을 위와 같이 정의한 상태에서 errorArr을 새로 정의해보면, 우리가 처음에 기대했던 대로 정확하지 않은 에러 객체에 대해 타입 에러가 발생하는 것을 확인할수 있다.

### 4.3.3 식별할 수 있는 유니온의 판별자 선정

- 식별할 수 있는 유니온을 사용할 때 주의할 점이 있다. **식별할 수 있는 유니온의 판별자는 유닛 타입으로 선언되어야 정상적으로 동작한다. 유닛 타입은 다른 타입으로 쪼개지지 않고 오직 하나의 정확한 값을 가지는 타입을 말한다.** null, undefined, 리터럴 타입을 비롯해 true, 1 등 정확한 값을 나타내는 타입이 유닛 타입에 해당한다. 반면에 다양한 타입을 할당할 수 있는 void, string, number와 같은 타입은 유닛 타입으로 적용되지 않는다.
- 공식 깃허브의 이슈 탭을 살펴보면 식별할 수 있는 유니온의 판별자로 사용할 수 있는 타입을 다음과 같이 정의하고 있다.
    - 리터럴타입이어야핸다.
    - 판별자로 선정한 값에 적어도 하나 이상의 유닛 타입이 포함되어야 하며, 인스턴스화할 수 있는 타입은 포함되지 않아야 한다.

### 4.4 Exhaustiveness Checking으로 점확한 타입 분기 유지하기

- Exhaustiveness는 사전적으로 철저함, 완전함을 의미한다. 따라서 Exhaustiveness Checking은 모든 케이스에 대해 철저하게 타입을 검사하는 것을 말하며 타입 좁히기에 사용되는 패러다임 중 하나다.
- 타입 가드를 사용해서 타입에 대한 분기 처리를 수행하면 필요하다고 생각되는 부분만 분기 처리를 하여 요구 사항에 맞는 코드를 작성할 수 있게 된다. 하지만 때로는 모든 케이스에 대해 분기 처리를 해야만 유지보수 측면에서 안전하다고 생각되는 상황이 생긴다. 이때 Exhaustiveness Checking을 통해 모든 케이스에 대한 타입 검사를 강제할 수 있다. 예시를 보며 Exhaustiveness Checking의 의미를 이해해보자.

### 4.4.1 상품권

- 배달의민족의 선물하기 서비스에는 다양한 상품권이 있다. 상품권 가격에 따라 상품권 이름을 반환해주는 함수를 작성하면 다음과 같다.

```js
type Productprice = "10000" | "20000"；
const getProductName = (productPrice： ProductPrice)： string => {
    if (productprice === "10000") return "배민상품권 1만 원";
    if (productPrice === "20000") return "배민상품권 2만 원";
    else {
        return "배민상품권";
    }
}；
```

- 여기까지는 각 상품권 가격에 따라 상품권 이름을 올바르게 반환하고 있어 큰 문제가 없다고 느껴질 수 있다. 하지만 새로운 상품권이 생겨서 Productprice 타입이 업데이트되어야 한다고 해보자.

```js
type Productprice = "10000" | "20000" | "5000";
const getProductName = (productPrice： ProductPrice)： string => {
    if (productprice === "10000") return "배민상품권 1만 원";
    if (productPrice === "20000") return "배민상품권 2만 원";
    if (productprice === "5000") return "배민상품권 5천 원"; // 조건 추가 필요
    else {
        return "배민상품권";
    }
}；
```

- 이처럼 Productprice 타입이 업데이트되었을 때 getProductName 함수도 함께 업데이트되어야 한다. prod나ctPrice가 "5000"일 경우의 조건도 검사하여 의도한 대로 상품권 이름을 반환해야 한다. 그러나 **getProductName 함수를 수정하지 않아도 별도 에러가 발생하는 것이 아니기 때문에 실수할 여지가 있다.** 이와 같이 모든 타입에 대한 타입 검사를 강제하고 싶다면 다음과 같이 코드를 작성하면 된다.

```js
type Productprice = "10000" | "20000" | "5000";
const getProductName = (productPrice： ProductPrice)： string => {
    if (productprice === "10000") return "배민상품권 1만 원";
    if (productPrice === "20000") return "배민상품권 2만 원";
    // if (productprice === "5000") return "배민상품권 5천 원"; // 조건 추가 필요
    else {
        exhaustiveCheck(productPrice); // Error： Argument of type 'string' is not assignable to parameter of type 'never'
        return "배민상품권";
    }
}；

const exhaustiveCheck = (param： never) => {
    throw new Error("type error!");
}；
```

- 앞의 코드에서 productPrice가 "5000"일 때의 분기 처리가 주석 처리된 것이 보일 것이다. 그리고 exhaustiveCheck(productPrice);에서 에러를 뱉고 있는데 ProductPrice 타입 중 5000이라는 값에 대한 분기 처리를 하지 않아서 (철저하게 검사하지 않았기 때문에) 발생한 것이다. 이렇게 모든 케이스에 대한 타입 분기 처리를 해주지 않았을 때, 컴파일타임 에러가 발생하게 하는 것을 Exhaustiveness Checking이라고 한다.
- 좀 더 자세히 살펴보면 exhaustiveCheck라는 함수가 보일 것이다. 이 **함수는 매개변수를 never 타입으로 선언하고 있다. 즉, 매개변수로 그 어떤 값도 받을 수 없으며 만일 값이 들어온다면 에러를 내뱉는다.** 이 함수를 타입 처리 조건문의 마지막 else문에 사용하면 앞의 조건문에서 모든 타입에 대한 분기 처리를강제할 수 있다.
- 이렇게 Exhaustiveness Checking을 활용하면 예상치 못한 런타임 에러를 방지하거나 요구사항이 변경되었을 때 생길 수 있는 위험성을 줄일 수 있다. 타입에 대한 철저한 분기 처리가 필요하다면 Exhaustiveness Checking 패턴을 활용해보길 바란다.

<h3>끝!</h3>
