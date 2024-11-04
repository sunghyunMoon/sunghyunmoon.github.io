---
title: 5장 타입 활용하기
categories:
- 우아한 타입스크립트 with 리액트
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/elegant_typescript.png"
---

- 우아한형제들의 실무 코드 예시를 살펴보면서 정확한 타이핑을 하지 못해 발생하는 문제를 타입스크립트의 다양한 기법과 유틸리티 타입을 활용해 해결해본다. 5장의 내용은 타입스크립트의 기본적인 문법과 리액트 및 react-query의 코드를 포함하고 있다. 하지만 기본적인 관련 지식만 알고 있다면 읽는 데 무리가 없을것이다.

### 5.1 조건부 타입

- 프로그래밍에서는 다양한 상황을 다루기 위해 조건문을 많이 활용한다. 타입도 마찬가지로 조건에 따라 다른 타입을 반환해야 할 때가 있다. 타입스크립트에서는 조건부 타입을 사용해 조건에 따라 출력 타입을 다르게 도출할 수 있다. 타입스크립트의 조건부 타입은 자바스크립트의 삼항 연산자와 동일하게 Condition ? A : B 형태를 가지는데 A는 Condition이 true일 때 도출되는 타입이고, B는 false일 때 도출되는 타입이다.
- 조건부 타입을 활용하면 중복되는 타입 코드를 제거하고 상황에 따라 적절한 타입을 얻을 수 있기 때문에 더욱 정확한 타입 추론을 할 수 있게 된다. 이 절에서는 extends, infer, never 등을 활용해 원하는 타입을 만들어보며 어떤 상황에서 조건부 타입이 필요한지 알아본다. 또한 **조건부 타입을 적용했을 때 어떤 장점을 얻을 수 있는지 알아보자.**

#### 5.1.1 extend와 제네릭을 활용한 조건부 타입

- **extends 키워드는 타입스크립트에서 다양한 상황에서 활용된다. 타입을 확장할 때와 타입을 조건부로 설정할 때 사용되며, 제네릭 타입에서는 한정자 역할로도 사용된다.** extends를 사용한 조건부 타입의 활용 예시를 보기 전에 간단하게 extends가 어떻게 조건부 타입으로 사용되는지 알아보자.

```js
T extends U ? X : Y
```

- 조건부 타입에서 extends를 사용할 때는 자바스크립트 삼항 연산자와 함께 쓴다. 이 표현은 타입 T를 U에 할당할 수 있으면 X 타입, 아니 면 Y 타입으로 결정됨을 의미한다. 간단한 예시를 통해 좀 더 면밀히 살펴보자.

```js
interface Bank {
financialCode: string;
companyName： string;
name： string;
fuIlName; string;
}

interface Card {
financialCode： string;
companyName： string;
name: string;
appCardType?: string;
}

type PayMethod<T> = T extends "card" ? Card : Bank;
type CardPayMethodType = PayMethod<"card">;
type BankPayMethodType = PayMethod<"bank">;
// extends 키워드를 일반적으로 문자열 리터럴과 함께 사용하지는 않지만. 예시에서는 extends의 활용법을 설명하기 위해 문자열 리터럴에 사용되고 있다.
```

- PayMethod 타입은 제네릭 타입으로 extends를 사용한 조건부 타입이다. 제네릭 매개변수에 "card"가 들어오면 Card 타입, 그 외의 값이 들어오면 Account 타입으로 결정된다. PayMethod를 사용해서 CardPayMethodType, BankPayMethodType을 도출할 수 있다. 이제 어떤 상황에서 조건부 타입을 효율적으로 사용할 수 있는지 알아보자.

#### 5.1.2 조건부 타입을 사용하지 않았을 때의 문제점

- 조건부 타입을 사용하기 전에 어떤 이슈가 있었는지 알아보자. 아래는 react-query（리액트 쿼리）를 활용한 예시다. 계좌, 카드, 앱 카드 등 3가지 결제 수단 정보를 가져오는API가 있으며, API의 엔드포인트는 다음과 같다고 해보자.

```js
계좌 정보 엔드포인트: www.baemin .com/baeminpay/.../bank
카드 정보 엔드포인트: www.baemin.com/baeminpay/.../card
앱 카드 정보 엔드포인트: www.baemin.com/baeminpay/.../appcard
```

- 각 API는 계좌, 카드, 앱카드의 결제 수단 정보를 배열 형태로 반환한다. 3가지 API의 엔드포인트가 비슷하기 때문에 서버 응답을 처리하는 공통 함수를 생성하고, 해당 함수에 타입을 전달하여 타입별로 처리 로직을 구현할 것이다.
- 함수를 구현하기 앞서 이전에 사용되는 타입들을 서버에서 받아오는 타입, UI 관련 타입 그리고 결제 수단별 특성에 따라 구분하여 살펴보자.
    - PayMethodBaseFromRes: 서버에서 받아오는 결제 수단 기본 타입으로 은행과 카드에 모두 들어가 있다
    - Bank, Card: 은행과 카드 각각에 맞는 결제 수단 타입이다. 결제 수단 기본 타입인 PayMethodBaseFromRes를 상속받아 구현한다.
    - PayMethodlnterface： 프론트에서 관리하는 결제 수단 관련 데이터로 UI를 구현하는 데 사용되는 타입이다.
    - PayMethodInfo<T extends Bank \| Card> :
        - 최종적인 은행, 카드 결제 수단 타입이다. 프론트에서 추가되는 UI 데이터 타입과 제네릭으로 받아오는 Bank 또는 Card를 합성한다.
        - extends를 제네릭에서 한정자로 사용하여 Bank 또는 Card를 포함하지 않는 타입은 제네릭으로 넘겨주지 못하게 방어한다.
        - BankPayMethodInfo = PayMethodlnterface & Bank처럼 카드와 은행의 타입을 만들어줄 수 있지만 제네릭을 활용해서 중복된 코드률 제거한다.

```js
interface PayMethodBaseFromRes {
    financialCode： string;
    name： string;
}
interface Bank extends PayMethodBaseFromRes {
    fullName： string;
}
interface Card extends PayMethodBaseFromRes {
    appCardType?: string;
}
type PayMethodInfo<T extends Bank | Card> = T & PayMethodlnterface;
type PayMethodlnterface = {
    companyName： string;
    // ...
}
```

- 이제 react-query의 useQuery를 사용하여 구현한 커스텀 훅인 useGetRegisteredList 함수를 살펴보자.
- useGetRegisteredList 함수는 useQuery의 반환 값을 돌려준다. useCommonQuery\<T>는 useQuery를 한 번 래핑해서 사용하고 있는 함수로 useQuery의 반환 data를 T타입으로 반환한다. fetcherFactory는 axios를 래핑해주는 함수이며, 서버에서 데이터를 받아온 후 onSuccess 콜백 함수를 거친 결괏값을 반환한다.

```js
type PayMethodType = PayMethodInfo<Card> | PayMethodInfo<Bank>;
export const useGetRegisteredList = (
type： "card" | "appcard" | "bank"
): UseQueryResult<PayMethodType[]> => {
    const url = `baeminpay/codes/${type === "appcard" ? "card" : type}`;

    const fetcher = fetcherFactory<PayMethodType[]>({
        onSuccess： (res) => {
            //  res는 PayMethodType[] 타입으로, 이는 PayMethodInfo<Card>[] 또는 PayMethodInfo<Bank>[]의 배열
            const usablePocketList =
            res?.filter(
            (pocket： PocketInfo<Card> | PocketInfo<Bank>) =>
            pocket?.useType === "USE"
            ) ?? []；
            // res 배열에서 PocketInfo<Card> 또는 PocketInfo<Bank> 타입의 요소 중 useType이 "USE"인 요소만 남기고 usablePocketList 배열로 반환
            return usablePocketList;
        },
    })；

    const result = useCommonQuery<PayMethodType[]>(url, undefined, fetcher);
    return result;
};
```

- 즉, useGetRegisteredList 함수는 타입으로 "card", "appcard", "bank"를 받아서 해당 결제 수단의 결제 수단 정보 리스트를 반환하는 함수이다. 함수 useGetRegisteredList가 반환하는 데이터는 UseQueryResult\<PayMethodType[]>이다. PayMethodType은 PayMethodInfo\<Card>와 PayMethodInfo\<Bank>의 유니온 타입이므로, 이 함수가 반환하는 데이터는 PayMethodInfo\<Card>와 PayMethodInfo \<Bank>의 배열이다. 그러나 필터링을 통해 반환된 데이터는 PocketInfo\<Card> | PocketInfo\<Bank> 타입의 요소를 포함하게 된다.
- 따라서, 최종적으로 useGetRegisteredList 함수가 반환하는 데이터의 타입은 PocketInfo<Card>[] | PocketInfo<Bank>[] 요소를 포함할 수 있는 UseQueryResult<PayMethodType[]>이다.
- **인자로 넣는 타입에 알맞은 타입을 반환하고 싶지만, 타입설정이 유니온으로만 되어있기 때문에 타입스크립트는 해당 타입에 맞는 Data 타입을 추론할 수없다.** 이처럼 인자에 따라 반환되는 타입을 다르게 설정하고 싶다면 extends를 사용한 조건부 타입을 활용하면 된다.

#### 5.1.3 extends 조건부 타입을 활용하여 개선하기

- useGetRegisteredList 함수의 반환 Data는 인자 타입에 따라 정해져 있다. 타입으로 "card" 또는 "appcard"를 받으면 카드 결제 수단 정보 타입인 Pocketlnfo<card>를 반환하고, "bank"를 받는다면 Pocketlnfo<bank>를 반환한다.
    - type: "card" \| "appcard => PocketInfo<Card>
    - type: "bank" => PocketInfo<Bank>

- 이러한 상황에서 조건부 타입을 활용하면 하나의 API 함수에서 타입에 따른 정확한 반환 타입을 추론하게 만들 수 있다.
- 조건부 타입을 사용해서 PayMethodInfo\<Card> | PayMethodInfo\<Bank> 타입이었던 PayMethodType 타입을 개선해보자.

```js
type PayMethodType<T extends "card" | "appcard" | "bank"> = T extends
| "card"
| "appcard"
? Card
: Bank;
```

- PayMethodType의 제네릭으로 받은 값이 "card" 또는 "appcard"일 때는 PayMethodlnfo\<Card> 타입을, 아닐 때는 PayMethodInfo\<Bank> 타입을 반환하도록 수정했다. 또한 결제 수단 타입에는 "card", "appcard". "bank"만 들어올 수 있기 때문에 **extends를 한정자로 활용해서 제네릭에 넘 겨오는 값을 제한**하도록 했다.
- 새롭게 정의한 PayMethodType 타입에 제네릭 값을 넣어주기 위해서는 useGetRegisteredList 함수 인자의 타입을 넣어줘야 한다. useGetRegisteredList 인자 타입을 제네릭으로 받으면서 extends를 활용하여 "card", "appcard", "bank" 이외에 다른 값이 인자로 들어올 경우에는 타입 에러를 반환하도록 구현해보자.

```js
export const useGetRegisteredList = <T extends "card" | "appcard" | "bank">(
    type:T
): UseQueryResult<PayMethodType<T>[]> => {
    const url = `baeminpay/codes/${type === "appcard" ? "card" : type}`;

    const fetcher = fetcherFactory<PayMethodType<T>>({
        // PayMethodType<T> 타입은 T가 "card" 또는 "appcard"일 때 PayMethodInfo<Card>, T가 "bank"일 때 PayMethodInfo<Bank>를 반환
        onSuccess： (res) => {
            const usablePocketList =
            res?.filter(
            (pocket： PocketInfo<Card> | PocketInfo<Bank>) =>
            pocket?.useType === "USE"
            ) ?? []；
            return usablePocketList;
            // 이 필터링된 결과는 PayMethodType<T>[] 타입이다. 즉, T가 "card" 또는 "appcard"인 경우 PayMethodInfo<Card>[], T가 "bank"인 경우 PayMethodInfo<Bank>[]가 반환된다.
        },
    })；

    const result = useCommonQuery<PayMethodType[]>(url, undefined, fetcher);
    return result;
};
```

- 조건부 타입을 활용하여 PayMethodType이 사용자가 인자에 넣는 타입 값에 맞는 타입만을 반환하도록 구현했다. 이제 인자로 "card" 또는 "appcard"를 넣는다면 PocketInfo<Card>를 반환하고, "bank"를 넣는다면 PocketInfo<Bank>를 반환한다.
- 지금까지 타입 확장을 제외한 타입에서 다양한 extends 활용 사례를 살펴봤다. extends 활용 예시는 크게 다음과 같이 정리할 수 있다.
    - 제네릭과 extends를 함께 사용해 제네릭으로 받는 타입을 제한했다. 따라서 개발자는 잘못된 값을 넘길 수 없기 때문에 휴먼 에러를 방지할수 있다.
    - extends를 활용해 조건부 타입을 설정했다. 조건부 타입을 사용해서 반환 값을 사용자가 원하는 값으로 구체화할수 얐었다. 이에 따라불필요한 타입 가드, 타입 단언 등을 방지할수 있다.

#### 5.1.4 infer를 활용해서 타입 추론하기

- extends를 사용할 때 infer 키워드를 사용할 수 있다. infer는 ‘추론하다’라는 의미를 지니고 있는데 타입스크립트에서도 단어 의미처럼 타입을 추론하는 역할을 한다. 삼항 연산자를 사용한 조건문의 형태를 가지는데, extends로 조건을 서술하고 infer로 타입을 추론하는 방식을 취한다.
- infer를 활용한 예시를 살펴보자.

```js
 type UnpackPromise<T> = T extends Promise<infer K>[] ? K : any;
```

- UnpackPromise 타입은 제네릭으로 T를 받아 **T가 Promise로 래핑된 경우라면 K를 반환하고,그렇지 않은 경우에는 any를 반환**한다. **Promise\<infer K>는 Promise의 반환 값을 추론해 해당 값의 타입을 K로 한다는 의미**이다.
- 이번에는 배민 라이더를 관리하는 라이더 어드민 서비스에서 사용하는 타입으로 infer를 살펴보자.

```js
interface RouteBase {
    name: string;
    path: string;
    component： ComponentType;
}

export interface Routeitem {
    name： string;
    path： string;
    component?: ComponentType;
    pages?： RouteBase[];
}

export const routes： Routeitem[] = [
    {
        name： "기기 내역 관리",
        path： "/device-history",
        component： DeviceHistoryPage,
    },
    {
        name： "헬멧 인증 관리",
        path： "/helmet-certification",
        component: HelmetCertificationPag,
    },
    //...
];
```

- RouteBase와 Routeitem은 라이더 어드민에서 라우팅을 위해 사용하는 타입이다. routes같이 배열 형태로 사용되며, 권한 API로 반환된 사용자 권한과 name을 비교하여 인가되지 않은 사용자의 접근을 방지한다. Routeitem의 name은 pages가 있을 때는 단순히 이름의 역할만하며 그렇지 않을 때는 사용자 권한과 비교한다.

```js
export interface SubMenu {
    name： string;
    path: string;
}

export interface MainMenu {
    name： string;
    path?： string;
    subMenus?： SubMenu[]；
}

export type Menultem = MainMenu | SubMenu;
export const menuList: MenuItem[] = [
    {
        name： "계정 관리",
        subMenus: [
                {
                name： "기기 내역 관리",
                path： "/device-history",
            },
            {
                name： "헬멧 인증 관리",
                path： "/helmet-certification",
            },
        ],
    },
    {
        name： "운행 관리",
        path： "/operation",
    },
//...
];
```

- MainMenu와 SubMenu는 메뉴 리스트에서 사용하는 타입으로 권한 API를 통해 반환된 사용자 권한과 name을 비교하여 사용자가 접근할 수 있는 메뉴만 렌더링한다. MainMenu의 name은 subMenus를 가지고 있을 때 단순히 이름의 역할만 하며, 그렇지 않을 때는 권한으로 간주된다.
- menuList에는 MainMenu와 SubMenu 타입이 올 수 있기 때문에 유니온 타입인 Menuitem를 정의하여 사용하고 있다. 따라서 menuList에서 subMenus가 없는 MainMenu의 name(권한)과 subMenus에서 쓰이는 name(권한), route name(권한)에 동일한 문자열만 입력해야 한다는 제약이 존재한다.
- 하지만 앞서 말한 바와 같이 name은 string 타입으로 정의되어 있기 때문에 routes와 menuList에서 subMenus의 기기 내역 관리처럼 서로 다른 값이 입력되어도 컴파일타임에서 에러가 발생하지 않는다. 또한 런타임에서도 인가되지 않음을 안내하는 페이지를 보여주거나 메뉴 리스트를 렌더링하지 않는 정도에 그치기 때문에, 존재하지 않는 권한에 대한 문제로 잘못인지할수있다.

```js
vtype PermissionNames = "기기 정보 관리" | "안전모 인증 관리" | "운행 여부 조회"; // ...
```

- 이를 개선하기 위해 PermissionNames처럼 별도 타입을 선언하여 name을 관리하는 방법도 있지만, 권한 검사가 필요 없는 subMenus나 pages가 존재하는 name은 따로 처리해야 한다.
- 이때 infer와 불변 객체 (as const)를 활용해서 menuList 또는 routes의 값을 추출하여 타입으로 정의하는 식으로 개선할 수 있다. 여기에서는 menuList 값을 추출하는 예시를 소개한다.

```js
export interface MainMenu {
    // ...
    subMenus?： ReadonlyArray<SubMenu>;
}

export const menuList = [
// ...
] as const;

interface RouteBase {
    name： PermissionNames;
    path： string;
    component： ComponentType;
}

// Routeitem은 두 가지 형태로 가질 수 있는 유니온 타입
export type Routeitem =
| {
    name： string;
    path： string;
    component?： ComponentType;
    pages： RouteBase[];
}
| {
    name： PermissionNames;
    path: string;
    component?: ComponentType;
};
```

- 먼저 subMenus의 타입을 ReadonlyArray로 변경하고, menuList에 as const 키워드를 추가하여 불변 객체로 정의한다. Route 관련 타입의 name은 menuList의 name에서 추출한 타입인 PermissionNames만 올 수 있도록 변경한다.

```js
type UnpackMenuNames<T extends ReadonlyArray<MenuItem>> = T extends
ReadonlyArray<infer U>
    ? U extends MainMenu
        ? U["subMenus"] extends infer V
            ? V extends ReadonlyArray<SubMenu>
                ? UnpackMenuNames<V>
                : U["name"]
            : never
        : U extends SubMenu
    ? U["name"]
    : never
: never;
```

- 자세히 위 코드를 이해해보자.
- T extends ReadonlyArray\<infer U>
    - T가 ReadonlyArray 타입인 경우, U는 배열의 요소 타입
    - 예를 들어, T가 ReadonlyArray<MainMenu \| SubMenu>라면 U는 MainMenu \| SubMenu 타입
- U extends MainMenu
    - U가 MainMenu 타입인 경우를 처리
    - MainMenu는 선택적 subMenus 속성을 포함할 수 있음
- U["subMenus"] extends infer V
    - U의 subMenus 속성을 V 타입으로 추론
    - V는 ReadonlyArray\<SubMenu> 타입
- V extends ReadonlyArray\<SubMenu>
    - V가 ReadonlyArray\<SubMenu> 타입인 경우를 처리
    - V가 ReadonlyArray\<SubMenu>라면 UnpackMenuNames\<V>를 재귀적으로 호출
    - 재귀 호출을 통해 SubMenu의 이름을 추출
- : U["name"]
    - V가 ReadonlyArray\<SubMenu>가 아닌 경우, U["name"]을 반환
    - 이는 MainMenu의 이름을 의미

- 구체적인 예시를 살펴보자.

```js
const mainMenu: MainMenu = {
    name: "MainMenu1",
    subMenus: [
        { name: "SubMenu1" },
        { name: "SubMenu2" }
    ]
};

type MenuNames = UnpackMenuNames<readonly [typeof mainMenu]>;
// MenuNames 타입은 "SubMenu1" | "SubMenu2"
```

- T는 readonly [MainMenu] 타입
- U는 MainMenu 타입
- U["subMenus"]는 ReadonlyArray<SubMenu> 타입
- 따라서 V는 ReadonlyArray<SubMenu> 타입으로 추론
- 재귀 호출
    - UnpackMenuNames<V>는 UnpackMenuNames<ReadonlyArray<SubMenu>>
    - 이제 T는 ReadonlyArray<SubMenu> 타입
    - 이로 인해 U는 SubMenu 타입
    - U extends SubMenu가 성립
    - U["name"]가 추출
- 이 과정을 통해 UnpackMenuNames는 MainMenu 객체의 subMenus 배열에서 모든 SubMenu의 이름을 추출
- PermissionNames는 menuList에서 권한으로 유효한 값만 추출하여 배열로 반환하는 타입임
을 확인할 수 있다.

### 5.2 템플릿 리터럴 타입 활용하기

- 타입스크립트에서는 유니온 타입을 사용하여 변수 타입을 특정 문자열로 지정할 수 있다.

```js
 type HeaderTag = "h1" | "h2" | "h3" | "h4" | "h5"；
```

- 이 기능을 사용하면 컴파일타임의 변수에 할당되는 타입을 특정 문자열로 정확하게 검사하여 휴먼 에러를 방지할 수 있고, 자동 완성 기능을 통해 개발 생산성을높일 수 있다.
- 타입스크립트 4.1부터 이를 확장하는 방법인 템플릿 리터 럴 타입(Template Literal Types)을 지원하기 시작했다. 템플릿 리터럴 타입은 자바스크립트의 템플릿 리터럴 문법을 사용해 특정 문자열에 대한 타입을 선언할 수 있는 기능이다. 앞 예시의 HeaderTag 타입은 템플릿 리터럴 타입을 사용하여 다음과 같이 선언할 수 있다.

```js
type HeadingNumber = 1 | 2 | 3 | 4 | 5;
type HeaderTag = `h${HeadingNuinber}`;
```

- 수평 또는 수직 방향을 표현하는 Direction 타입을 다음과 같이 표현할수 있다.

```js
type Direction =
    | "top"
    | "topLeft"
    | "topRight"
    | "bottoni"
    | "bottomLeft"
    | "bottomRight";
```

- 이 코드의 Direction 타입은 "top", "bottom", "left", "right"가 합쳐진 문자열로 선언되어 있다. 이 코드에 템플릿 리터럴 타입을 적용하면 다음과 같이 좀 더 명확하게 표시할 수 있다.

```js
type Vertical = "top" | "bottom";
type Horizon = "Left" | "right";

type Direction = Vertical | `${Vertical}${Capitalize<Horizon>}`;
```

- 따라서 템플릿 리터럴 타입을 사용하면 더욱 읽기 쉬운 코드로 작성할 수 있게 되며. 코드를 재사용하고 수정하는 데 용이한 타입을 선언할 수 있다.

### 5.3 커스텀 유틸리티 타입 활용하기

- 타입스크립트로 프로젝트를 진행하다 보면 표현하기 힘든 타입을 마주할 때가 있다. 원하는 타입을 정확하게 설정해야만 해당 컴포넌트, 함수의 안정성과 사용성을 높일 수 있지만 타입스크립트에서 제공하는 유틸리티 타입만으로는 표현하는 데 한계를 느끼기도 한다. 이럴 때는 유틸리티 타입을 활용한 커스텀 유틸리티 타입을 제작해서 사용하면 된다. 이 절에서는 우아한형제들에서 사용하는 유틸리티 함수와 커스텀 유틸리티 타입을 소개하면서 어떻게 유틸리
티 타입을 활용하는지를 살펴본다.

#### 5.3.1 유틸리티 함수를 활욤해 styled-components의 중복 타입 선언 피하기

- 리액트 컴포넌트를 구현할 때 여러 옵션을 props로 받아 유연한 컴포넌트로 만들 수 있다. 예를 들어 컴포넌트의 background-color, size 값을 props로 받아와서 상황에 맞게 스타일을 구현할 때가 많다. 이와 같은 스타일 관련 props는 styled—components에 전달되며 styled-components에도 해당 타입을 정확하게 작성해줘야 한다. styled-components로 만든 컴포넌트에 넘겨주는 타입은 props에서 받은 타입과 동일할 때가 대부분이다. 이런 경우에는 타입스크립트에서 제공하는 Pick, Omit 같은 유틸리티 타입을 잘 활용하여 코드를 간결하게 작성할 수 있다.
- 먼저 유틸리티 타입을 활용하지 않으면 어떤 불편함이 발생하는지 살펴보자.

<h5>Props 타입과 styled-components 타입의 중복 선언 및 문제점</h5>

- 아래 컴포넌트는 수평선을 그어주는 Hr 컴포넌트다.

```js
// HrComponent.tsx
export type Props = {
    height?： string;
    color?： keyof typeof colors;
    isFull?: boolean;
    className?: string;
    ...
}

export const Hr： VFC<Props> = ({ height, color, isFull, className }) => {
    ...
    return <HrComponent height={height} color={color} isFull={isFull}
    className={class Name} />;
}；

// styles.ts
import { Props } from "...";
// Props 타입에서 'height', 'color', 'isFull' 프로퍼티만 선택하여 StyledProps 타입을 정의
type StyledProps = Pick<Props, 'height' | 'color' | 'isFull'>;
// HrComponent를 스타일링
const HrComponent = styled.hr<StyledProps>`
    height： ${({ height }) => height || "10px"};
    margin： 0;
    background-color： ${({ color }) => colors[color || "gray7"];
    border： none;
    ${({ isFull }) =>
    isFull &&
    css'
    margin: 0 -15px;
    `}
`;
```

- StyledProps 타입 정의: Props 타입에서 height, color, isFull 프로퍼티만 선택하여 StyledProps 타입을 정의하고 있다.
- HrComponent 스타일링: styled-components 라이브러리를 사용하여 hr 태그를 스타일링하고 있다.
- Hr 컴포넌트 Props의 height, color, isFull 속성은 styled-components 컴포넌트인 HrComponent에 바로 연결되며 타입도 역시 같다. // style.ts 주석 아래 코드처럼 height, color, isFulll에 대한 StyledProps 타입을 새로 정의하여 HrComponent에 적용해보자.
- **StyledProps를 따로 정의하려면 Props와 똑같은 타입임에도 새로 작성해야 하므로 불가피하게 중복된 코드가 생긴다. 그리고 Props의 height, color, isFull 타입이 변경되면 StyledProps도 같이 변경돼야 한다.** Hr 컴포넌트가 간단한 컴포넌트이기 때문에 코드를 중복해서 작성하는 게 별로 번거롭지 않을 수도 있지만. 컴포넌트가 더 커지고 styled-components로 만든 컴포넌트가 늘어날수록 중복되는 타입이 많아지며 관리해야 하는 포인트도 늘어난다.
- 이런 문제를 Pick, Omit 같은 유틸리티 타입으로 개선할 수 있다. 아래 그림처럼 Pick 유틸리티 타입을 사용해서 styled-components 컴포넌트 타입을 작성해보자.
- 직접 선언한 props 타입(before)

```js
type Props = {
    height?： string | undefined;
    color?： "red" | "blue" | "yellow" | "green";
    isFull?: boolean | undefined;
    className?: string | undefined;
}

export type Props = {
    height?： string;
    color?： keyof typeof colors;
    isFull?: boolean;
    className?: string;
}
```

- 유틸리티 타입 Pick을 사용하여 선언한 StyledProps 타입

```js
type styledProps = {
    height?： string | undefined;
    color?： "red" | "blue" | "yellow" | "green";
    isFull?: boolean | undefined;
}

type StyledProps = Pick<Props, 'height' | 'color' | 'isFull'>;
```

- 이처럼 Pick 유틸리티 타입을 활용해서 props에서 필요한 부분만 선택하여 styled-components 컴포넌트의 타입을 정의하면, 중복된 코드를 작성하지 않아도 되고 유지보수를 더욱 편리하게 할 수 있게 된다. 이외에도 상속받는 컴포넌트 혹은 부모 컴포넌트에서 자식 컴포넌트로 넘겨주는 props 등에도 Pick, Omit 같은유틸리티 타입을 활용할수 있다.

#### 5.3.2 PickOne 유틸리티 함수

- 타입스크립트에는 서로 다른 2개 이상의 객체를 유니온 타입으로 받을 때 타입 검사가 제대로 진행되지 않는 이슈가 있다. 이런 문제를 해결하기 위해 PickOne이라는 이름의 유틸리티 함수를 구현해보자.
- 아래 코드와 같이 Card. Account 중 하나의 객체만 받고 싶은 상황에서 Card | Account로 타입을 작성하면 의도한 대로 타입 검사가 이뤄지지 않는다. 또한 withdraw 함수의 인자로 {card: 'hyundai'} 또는 {account: 'hana'} 중 하나만 받고 싶지만 실제로는 card, account 속성을 모두 받아도 타입 에러가 발생하지 않는다.

```js
type Card = {
    card: string
}；

type Account = {
    account： string
}；
function withdraw(type： Card | Account) {
    ...
}
withdraw({ card： "hyundai", account： "hana" });
```

- 왜 타입 에러가 발생하지 않을까? 그 이유는 앞서 언급한 바와 같이 집합 관점으로 볼 때 유니온은 합집합이 되기 때문이다. 따라서 card, account 속성이 하나씩만 할당된 상태도 허용하지만 card, account 속성이 모두 포함되어도 합집합의 범주에 들어가기 때문에 타입 에러가 발생하지 않는 것이다.
- 이런 문제를 해결하기 위해 타입스크립트에서는 식별할 수 있는 유니온 기법을 자주 활용한다.

<h5>식별할 수 있는 유니온으로 객체 타입을 유니온으로 받기</h5>

- 식별할 수 있는 유니온은 각 타입에 type이라는 공통된 속성을 추가하여 구분짓는 방법이다. 식별할 수 있는 유니온을 활용하면 공통된 속성인 type을 기준으로 객체를 구분할 수 있기 때문에 withdraw 함수를 사용하는 곳에서 정확한 타입을 추론할 수 있게 된다.

```js
type Card = {
    type： "card";
    card： string;
}；
type Account = {
    type： "account";
    account: string;
};
function withdraw(type: Card | Account) {
    ...
}
withdraw({ type： "card", card： "hyundai" });
withdraw({ type： "account", account： "hana" });
```

- 식별할 수 있는 유니온으로 문제를 해결할 수 있지만 일일이 type을 다 넣어줘야 하는 불편함이 생긴다. 처음부터 식별할 수 있는 유니온 형태로 설계했다면 불편함은 덜했을 수도 있지만, 이미 구현된 상태에서 식별할 수 있는 유니온을 적용하려면 해당 함수를 사용하는 부분을 모두 수정해야 한다. 실수로 수정하지 않은 부분이 생긴다면 또 다른 문제가 발생할 수 있다.
- 이러한 상황을 방지하기 위해 PickOne이라는 유틸리티 타입을 구현하여 적용해보자.

<h5>PickOne 커스텀 유틸리티 타입 구현하기</h5>

- 타입스크립트에서 제공하는 유틸리티 타입을 활용해서 커스텀 유틸리티 타입을 만들어보자. **앞의 여러 속성 중 하나의 속성만 받는 커스텀 유틸리티 타입을 구현하기 전에 구현해야 하는 타입이 정확히 무엇인지 생각해보자.**
- 구현하고자 하는 타입은 account 또는 card 속성 하나만 존재하는 객체를 받는 타입이다. 처음에 작성한 것처럼 { account： string } | { card： string }으로타입을 구현했을 때는 account와 card 속성을 모두 가진 객체도 허용되는 문제가 있었다.
- account일 때는 card를 받지 못하고, card일 때는 account를 받지 못하게 하려면 하나의 속성이 들어왔을 때 다른 타입을 옵셔널한 undefined 값으로 지정하는 방법을 생각해볼 수 있다. 

```js
{ account: string; card?： undefined } | { account?: undefined; card: string }
```

- 결국 선택하고자 하는 하나의 속성을 제외한 나머지 값을 옵셔널 타입 十 undefined로 설정하면 원하고자 하는 속성만 받도록 구현할 수 있다. 이를 커스텀 유틸리티 타입으로 구현해보면 아래와같다.

```js
type PickOne<T> = {
    [P in keyof T]： Record<P, T[P]> & Partial<Record<Exclude<keyof T, P>, undefined>>;
}[keyof T];
```

<h5>PickOne 살펴보기</h5>

- 앞의 유틸리티 타입을 하나씩 자세하게 뜯어보자. 먼저 PickOne 타입을 2가지 타입으로 분리해서 생각할 수 있다.

<h6>One<T></h6>

```js
 type One<T> = { [P in keyof T]： Record<P, T[P]> }[keyof T];
```

- keyof T
    - keyof T는 타입 T의 모든 키를 유니언 타입으로 반환한다.
    - 예를 들어, T가 { card: string; price: number }라면, keyof T는 'card' | 'price'가 된다.
- 맵드 타입 구문 [P in keyof T]
    - P는 T 타입의 모든 키를 순회한다.
    - 예를 들어, T가 { card: string; price: number }라면, P는 순차적으로 card와 price가 된다.
- Record<P, T[P]>
    - Record<K, T>는 TypeScript의 유틸리티 타입으로, **키 K와 값 T를 가진 객체 타입을 생성**한다.
    - 예를 들어, P가 card라면 Record<'card', string>은 { card: string } 타입이 된다.
- { [P in keyof T]: Record<P, T[P]> }
    - 이 구문은 타입 T의 각 키 P에 대해 Record<P, T[P]> 타입을 생성하여 새로운 객체 타입을 만든다.
    - 예를 들어, T가 { card: string; price: number }라면, { [P in keyof T]: Record<P, T[P]> }는 { card: Record<'card', string>; price: Record<'price', number> }가 된다.
    - 결국 { card: { card: string }; price: { price: number } } 타입이 된다.
- [keyof T]
    - 앞에서 생성한 타입에서 keyof T에 해당하는 모든 키를 유니언 타입으로 추출한다.
    - 예를 들어, T가 { card: string; price: number }라면, keyof T는 'card' | 'price'가 되고, **최종적으로 { card: { card: string }; price: { price: number } }['card' | 'price']는 { card: string } | { price: number } 타입이 된다.**
    - { card: { card: string }; price: { price: number } }['card']는 {card: string} 타입이고, { card: { card: string }; price: { price: number } }['price']는 { price: number } 타입이다. 따라서 인덱스 타입 조회를 각각 한 다음 유니온 타입으로 합친것과 같다.

- 정리
    - 1. T 타입의 모든 키를 순회한다.
    - 2. 각 키와 해당 값을 Record 타입으로 변환한다.
    - 3. 최종적으로 변환된 객체 타입에서 모든 키를 유니언 타입으로 추출한다.

```js
type Card = { card： string };
const one： One<Card> = { card： "hyundai" };
```

<h6>ExcludeOne<T></h6>

```js
type ExcludeOne<T> = { [P in keyof T]： Partial<Record<Exclude<keyof T, P>, undefined>> }[keyof T];
```

- Exclude<keyof T, P>
    - Exclude<UnionType, ExcludedMembers>는 유니언 타입에서 특정 멤버를 제외하는 타입 유틸리티이다.
    - Exclude<keyof T, P>는 T 타입의 키들 중 P를 제외한 나머지 키들을 반환한다.
    - 예를 들어, P가 card일 때, Exclude<'card' | 'price', 'card'>는 'price'가 된다.
- Record<Exclude<keyof T, P>, undefined>
    - Record<Exclude<keyof T, P>, undefined>는 P를 제외한 나머지 키들에 대해 값 타입이 undefined인 객체 타입을 생성한다.
    - 예를 들어, P가 card일 때, Record<'price', undefined>는 { price?: undefined }가 된다.
- Partial<T>
    - Partial<T>는 T 타입의 모든 프로퍼티를 선택적(Optional)으로 만드는 타입 유틸리티
    - Partial<Record<Exclude<keyof T, P>, undefined>>는 P를 제외한 나머지 키들에 대해 선택적 undefined 타입을 가진 객체 타입을 생성한다.
    - P가 card일 때, Partial<Record<'price', undefined>>는 { price?: undefined }가 된다.
- { [P in keyof T]： Partial<Record<Exclude<keyof T, P>, undefined>> }
    - T 타입의 각 키 P에 대해 P를 제외한 나머지 키들을 undefined로 설정한 객체 타입을 생성한다.
    - 최종적으로, [keyof T]를 사용하여 이들 객체 타입을 유니언 타입으로 합친다.
    - 예를 들어, T가 { card: string; price: number }일 때, 최종 타입은 { price?: undefined } | { card?: undefined }가 된다.

<h6>PickOne<T></h6>

```js
type PickOne<T> = One<T> & ExcludeOne<T>;
```

- One<T> & ExcludeOne<T>는 [P in keyof T]를 공통으로 갖기 때문에 아래 같이 교차된다.

```js
[P in keyof T]: Record<P, T[P]> & Partial<Record<Exclude<keyof T, P>, undefined>>
```

- ) 이 타입을 해석하면 전달된 T 타입의 1 개의 키는 값을 가지고 있으며, 나머지 키는 옵셔널한 undefined 값을 가진 객체를 의미한다.

```js
type Card = { card： string };
type Account = { account： string };
const pickOnel： PickOne<Card & Account> = { card： "hyundai" }; // (0)
const pickOne2： PickOne<Card & Account> = { account： "hana" }; // (0)
const pickOne3： PickOne<Card & Account> = { card： "hyundai", account： undefined }; // (0)
const pickOne4; PickOne<Card & Account> = { card： undefined, account： "hana" }; // (0)
const pickOne5： PickOne<Card & Account> = { card： "hyundai", account; "hana" }; // (X)
```

- 지금까지 PickOne 타입을 구현하기 위해 단계별로 One<T> 타입과 ExcludeOne<T> 타입을 구현하는 예시를 살펴보았다. 유틸리티 타입만으로는 원하는 타입을 추출하기 어려울 때 커스텀 유틸리티 타입을 구현한다. 앞에서 본 예시에서처럼 커스텀 유틸리티 타입을 구현할 때는 정확히 어떤 타입을 구현해야 하는지를 파악하고, 필요한 타입을 작은 단위로 쪼개어 생각하여 단계적으로 구현하는 게 좋다. 이렇게 하면 용이하게 원하는 타입을 구현할 수 있을 것이다.

#### 5.3.3 NonNullable 타입 검사 함수를 사용하여 간편하게 타입 가드하기

- 타입 가드는 타입스크립트에서 많이 사용된다. 특히 null을 가질 수 있는값(Nullable)의 null 처리는 자주 사용되는 타입 가드 패턴의 하나이다.
- 일반적으로 if문을 사용해서 null 처리 타입 가드를 적용하지만, is 키워드와 NonNullable 타입으로 타입 검사를 위한 유틸 함수를 만들어서 사용할 수도 있다.

<h6>NonNullable 타입이란<T></h6>

- 타입스크립트에서 제공하는 유틸리티 타입으로 제네릭으로 받는 T가 null 또는 undefined 일 때 never 또는 T를 반환하는 타입이다. NonNullable을 사용하면 null이나 undefined가 아닌 경우를 제외할 수 있다.

```js
type NonNullable<T> = T extends null | undefined ? never : T;
```

<h6>null, undefined를 검사해주는 NonNullable 함수</h6>

- NonNullable 유틸리티 타입을 사용하여 null 또는 undefined를 검사해주는 타입 가드 함수를 만들어 쓸 수 있다.
- NonNullable 함수는 매개변수인 value가 null 또는 undefined라면 false를 반환한다. is 키워드가 쓰였기 때문에 NonNullable 함수를 사용하는 쪽에서 true가 반환된다면 넘겨준 인자는 null이나 undefined가 아닌 타입으로 타입 가드（타입이 좁혀진다）가 된다.

### 5.4 불변 객체 타입으로 활용하기

- 프로젝트를 진행하면서 상숫값을 관리할 때 객체를 사용한다.

```js
const colors = {
red： "#F45452",
green： "#0C952A",
blue: "#1A7CFF",
}；
const getColorHex = (key; string) => colors[key];
```

- 컴포넌트나 함수에서 이런 객체를 사용할 때 열린 타입으로 설정할 수 있다. 함수 인자로 키를 받아서 value를 반환하는 함수를 보자. 키 타입을 해당 객체에 존재하는 키값으로 설정하는 것이 아니라 string으로 설정하면 getColorHex 함수의 반환 값은 any가 된다. colors에 어떤 값이 추가될지 모르기 때문이다.
- 여기서 as const 키워드로 객체를 불변 객체로 선언하고, keyof 연산자를 사용하여 getColorHex 함수 인자로 실제 colors 객체에 존재하는 키값만 받도록 설정할 수 있다. keyof, as const로 객체 타입을 구체적으로 설정하면 타입에 맞지 않는 값을 전달할 경우 타입 에러가 반환되기 때문에 컴파일 단계에서 발생할 수 있는 실수를 방지할 수 있다. 즉, 이런 방법으로 객체 타입을 더 정확하고 안전하게 설정할 수 있다.

#### 5.4.1 Atom 컴포넌트에서 theme style 객체 활용하기

- Atom 단위의 작은 컴포넌트(Button, Header, Input 등)는 폰트 크기, 폰트 색상, 배경 색상 등 다양한 환경에서 유연하게 사용될 수 있도록 구현되어야 하는데 이러한 설정값은 props로 넘겨주도록 설계한다. props로 직접 색상 값을 직접 넘겨줄 수도 있지만 그렇게 하면 사용자가 모든 색상 값을 인지해야 하고, 변경 사항이 생길 때 직접 값을 넣은 모든 곳을 찾아 수정해야 하는 번거로움이 생기기 때문에 변경에 취약한 상태가 된다.
- 이런 문제를 해결하기 위해 **대부분의 프로젝트에서는 해당 프로젝트의 스타일 값을 관리해주는 theme 객체를 두고 관리**한다. Atom 컴포넌트에서는 theme 객체의 색상, 폰트 사이즈의 키값을 props로 받은 뒤 theme 객체에서 값을 받아오도록 설계한다. 컴포넌트에서 props의 color, fontsize 값의 타입을 정의할 때는 아래 예시처럼 string으로 설정할 수도 있다.

```js
interface Props {
    fontsize?： string;
    backgroundcolor?： string;
    color?: string;
    onClick: (event： React.MouseEvent<HTMLButtonElement>) => void | Promise<void>;
}
const Button: FC<Props> = ({ fontSize, backgroundColor, color, children }) => {
    return (
        <ButtonWrap
            fontSize={fontSize}
            backgroundColor={backgroundcolor}
            color={color}
        >
            {children}
        </ButtonWrap>
    );
};

const ButtonWrap = styled.button<Omit<Props, "onClick">>`
    color： ${({ color }) => theme.color[color ?? "default"]};
    background-color： ${({ backgroundcolor }) =>
        theme.bgColor[backgroundcolor ?? "default"]};
    font-size： ${({ fontsize }) => theme.fontSize[fontSize ?? "default"]};
`;
```

- 앞의 코드에서 fontsize, backgroundcolor 같은 props 타입이 string이면 Button 컴포넌트의 props로 color, backgroundcolor를 넘겨줄 때 키값이 자동 완성되지 않으며 잘못된 키값을 넣어도 에러가 발생하지 않게 된다. 이러한 문제는 theme 객체로 타입을 구체화해서 해결할 수 있다.
- theme 객체로 타입을 구체화하려면 keyof, typeof 연산자가 타입스크립트에서 어떻게 사용되는지 알아야한다.

<h6>타입스크립트 keyof 연산자로 객체의 키값을 타입으로 추출하기</h6>

- 타입스크립트에서 keyof 연산자는 객체 타입을 받아 해당 객체의 키값을 string 또는 number의 리터럴 유니온 타입을 반환한다.
- 아래 예시로 keyof의 동작을 확인해보자. ColorType 객체 타입의 keyof ColorType을 사용하면 객체의 키값인 ‘red’, green’, ‘blue’가 유니온으로 나오게 된다.

```js
interface ColorType {
red; string;
green： string;
blue： string;
}

type ColorKeyType = keyof ColorType; //'red' | 'green' | 'blue'
```

<h6>타입스크립트 typeof 연산자로 값을 타입으로 다루기</h6>

- keyof 연산자는 객체 타입을 받는다. 따라서 객체의 키값을 타입으로 다루려면 값 객체를 타입으로 변환해야 한다. 이때 타입스크립트의 typeof 연산자를 활용할 수 있다. **자바스크립트에서는 typeof가 타입을 추출하기 위한 연산자로 사용된다면, 타입스크립트에서는 typeof가 변수 혹은 속성의 타입을 추론하는 역할을 한다.**
- 타입스크립트의 typeof 연산자는 단독으로 사용되기보다 주로 ReturnType같이 유틸리티 타입이나 keyof 연산자같이 타입을 받는 연산자와 함께 쓰인다.
- typeof로 colors 객체의 타입을 추론한 결과는 아래와 같다.

```js
const colors = {
red: "#F45452",
green: "#0C952A",
blue: "#1A7CFF",
};

type ColorsType = typeof colors;
/**
{
red： string;
green： string;
blue： string;
}
*/
```

<h6>객체의 타입을 활용해서 컴포넌트 구현하기</h6>

- keyof, typeof 연산자를 사용해서 theme 객체 타입을 구체화하고, string으로 타입을 설정했던 Button 컴포넌트를 개선해보자.
- color, backgroundcolor, fontsize의 타입을 theme 객체에서 추출하고 해당 타입을 Button 컴포넌트에 사용했다.

```js
import { FC } from "react";
import styled from "styled-components";
const colors = {
black： "#000000",
gray： "#222222",
white: "#FFFFFF",
mint： "#2AC1BC",
};

const theme = {
    colors: {
        default： colors.gray,
        ...colors
    },
    backgroundcolor： {
        default： colors.white,
        gray： colors.gray,
        mint： colors.mint,
        black： colors.black.
    },
    fontsize： {
        default： "16px",
        small： "14px",
        large： "18px",
    },
}；

type ColorType = typeof keyof theme.colors;
type BackgroundColorType = typeof keyof theme.backgroundcolor;
type FontSizeType = typeof keyof theme.fontsize;

interface Props {
    color?： ColorType;
    backgroundcolor?： BackgroundColorType;
    fontsize?： FontSizeType;
    onClick： (event： React.MouseEvent서TMLButtonElement>) => void ! Promise<void>;
}

const Button: FC<Props> = ({ fontSize, backgroundColor, color, children }) => {
    return (
        <ButtonWrap
            fontSize={fontSize}
            backgroundColor={backgroundcolor}
            color={color}
        >
            {children}
        </ButtonWrap>
    );
};

const ButtonWrap = styled.button<Omit<Props, "onClick">>`
    color： ${({ color }) => theme.color[color ?? "default"]};
    background-color： ${({ backgroundcolor }) =>
        theme.bgColor[backgroundcolor ?? "default"]};
    font-size： ${({ fontsize }) => theme.fontSize[fontSize ?? "default"]};
`;
```

- Button 컴포넌트를 사용하는 곳에서 아래처럼 background의 값만 받을 수 있게 되었고 다른 값을 넣었을 때는 타입 오류가 발생한다.

### 5.5 Record 원시 타입 키 개선하기

- 객체 선언 시 키가 어떤 값인지 명확하지 않다면 Record의 키를 string이나 number 깉은 원시 타입으로 명시하곤 한다. 이때 타입스크립트는 키가 유효하지 않더라도 타입상으로는 문제 없기 때문에 오류를 표시하지 않는다. 이것은 예상치 못한 런타임 에러를 야기할 수 있다. 이 절에서는 Record를 명시적으로 사용하는 방안에 대해 다룬다.

#### 5.5.1 무한한 키를 집합으로 가지는 Record

- 다음처럼 음식 분류（한식, 일식）를 키로 사용하는 음식 배열이 담긴 객체를 만들었다.

```js
type Category = string;
interface Food {
    name： string;
    // ...
}

const foodByCategory： Record<Category, Food[]> = {
    한식: [{ name： "제육덮밥" }, { name： "뚝배기 불고기" }],
    일식: [{ name： "초밥" }, { name： "텐동" }],
}；
```

- 여기에서 Category의 타입은 string이다. Category를 Record의 키로 사용하는 foodByCategory 객체는 무한한 키 집합을 가지게 된다. 이때 foodByCategory 객체에 없는 키값을 사용하더라도 타입스크립트는 오류를 표시하지 않는다.

```js
foodByCategory["양식"]; // Food[]로 추론
foodByCategory["양식"].map((food) => console.log(food.name)); // 오류가 발생하지 않는다
```

- 그러나 foodByCategory["양식"]은 런타임에서 undefined가 되어 오류를 반환한다.
- 이때 자바스크립트의 옵셔널 체이닝 등을 사용해 런타임 에러를 방지할 수 있다.

```js
foodByCategory["양식"]?.map((food) => console.log(food.name));
```

- 그러나 어떤 값이 undefined인지 매번 판단해야 한다는 번거로움이 생긴다. 또한 실수로 undefined일 수 있는 값을 인지하지 못하고 코드를 작성하면 예상치 못한 런타임 에러가 발생할 수 있다. 하지만 타입스크립트의 기능을 활용하여 개발 중에 유효하지 않은 키가 사용되었는지 또는 undefined일 수 있는 값이 있는지 등을 사전에 파악할 수 있다.

#### 5.5.2 유닛 타입으로 변경하기

- 키가 유한한 집합이라면 유닛 타입（다른 타입으로 쪼개지지 않고 오직 하나의 정확한 값을 가지는 타입）을사용할수 있다.

```js
type Category = "한식" | "일식";
interface Food {
    name： string;
    // ...
}

const foodByCategory: Record<Category, Food[]> = {
    한식: [{ name： "제육덮밥" }, { name： "뚝배기 불고기" }],
    일식: [{ name： "초밥" }, { name： "텐동" }],
};

// Property ’양식' does not exist on type 'Record<Category, Food[]>'.
foodByCategory ["양식"];
```

- 이제 Category로 한식 또는 일식만 올 수 있기 때문에 양식을 키로 사용하면 에러가 발생한다. 이처럼 유닛 타입을 활용하면 개발 중에 유효하지 않은 키가 사용되었는지를 확인할 수 있다. 그러나 키가 무한해야 하는 상황에는 적합하지 않다.

#### 5.5.3 Partial을 활용하여 정확한 타입 표현하기

- 키가 무한한 상황에서는 Partial을 사용하여 해당 값이 undefined일 수 있는 상태임을 표현할 수 있다. 객체 값이 undefined일 수 있는 경우에 Partial을 사용해서 PartialRecord 타입을 선언하고 객체를 선언할 때 이것을 활용할 수 있다.

```js
type PartialRecord<K extends string, T> = Partial<Record<K, T>>;
type Category = string;
interface Food {
    name; string;
    // ...
}

const foodByCategory： PartialRecord<Category, Food[]> = {
    한식: [{ name： "제육덮밥" }, { name： "뚝배기 불고기" }],
    일식: [{ name: "초밥" }, { name： "텐동" }],
}；
foodByCategory["양식"]; // Food[] 또는 undefined 타입으로 추론
foodByCategory["양식"].map((food) => console.log(food.name)); // Object is possibly 'undefined'
foodByCategory["양식"]?.map((food) => console.log(food.name)); // OK
```

- Record<K, T>
    - Record<K, T>는 객체 타입을 생성하는 유틸리티 타입
    - 예를 들어, Record<'a' | 'b', number>는 { a: number; b: number } 타입을 생성한다.
    - K는 문자열 유니언 타입이어야 하며(K extends string), T는 값의 타입이다.
    - Record<K, T>는 K에 포함된 모든 키에 대해 값이 T인 객체 타입을 생성합니다.
- Partial<Record<K, T>>
    - Record<K, T>를 먼저 생성한 후, 이를 Partial로 감싸서 모든 프로퍼티를 선택적으로 만든다.
    - PartialRecord<K, T>는 Record<K, T>에서 모든 프로퍼티를 선택적으로 만든 객체 타입을 정의한다.

```js
type Keys = 'a' | 'b' | 'c';
type Value = number;

type Example = PartialRecord<Keys, Value>;
// Example은 { a?: number; b?: number; c?: number } 타입입니다
```

- 타입스크립트는 foodByCategory[key]를 Food[] 또는 undefined로 추론하고, 개발자에게 이 값은 undefined일 수 있으니 해당 값에 대한 처리가 필요하다고 표시해준다. 개발자는 안내를 보고 옵셔널 체이닝을 사용하거나 조건문을 사용하는 등 사전에 조치할 수 있게 되어 예상치 못한 런타임 오류를 줄일 수 있다.

<h3>끝!</h3>
