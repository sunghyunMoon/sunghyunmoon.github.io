---
title: 5장 타입 활용하기
categories:
- 우아한 타입스크립트 with 리액트
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/elegant_typescript.png"
---

- 우아한형제들의 실무 코드 예시를 살펴보면서 정확한 타이핑을 하지 못해 발생하는 문제를 타입스크립트의 다양한 기법과 유틸리티 타입을 활용해 해결해본다. 5장의 내용은 타입스크립트의 기본적인 문법과 리액트 및 react-query의 코드를 포함하고 있다. 하지만 기본적인 관련 지식만 알고 있다면 읽는 데 무리가 없을것이다.

### 5.1 조건부 타입

- 프로그래밍에서는 다양한 상황을 다루기 위해 조건문을 많이 활용한다. 타입도 마찬가지로 조건에 따라 다른 타입을 반환해야 할 때가 있다. 타입스크립트에서는 조건부 타입을 사용해 조건에 따라 출력 타입을 다르게 도출할 수 있다. 타입스크립트의 조건부 타입은 자바스크립트의 삼항 연산자와 동일하게 Condition ? A : B 형태를 가지는데 A는 Condition이 true일 때 도출되는 타입이고, 으는 false일 때 도출되는 타입이다.
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
계좌 정보 엔드포인트: WWW .baemin .com/baeminpay/.../bank
카드 정보 엔드포인트: WWW.baemin.com/baeminpay/.../card
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
- useGetRegisteredList 함수는 useQuery의 반환 값을 돌려준다. useCommonQuery<T>는 useQuery를 한 번 래핑해서 사용하고 있는 함수로 useQuery의 반환 data를 T타입으로 반환한다. fetcherFactory는 axios를 래핑해주는 함수이며, 서버에서 데이터를 받아온 후 onSuccess 콜백 함수를 거친 결괏값을 반환한다.

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

- 즉, useGetRegisteredList 함수는 타입으로 "card", "appcard", "bank"를 받아서 해당 결제 수단의 결제 수단 정보 리스트를 반환하는 함수이다. 함수 useGetRegisteredList가 반환하는 데이터는 UseQueryResult<PayMethodType[]>이다. PayMethodType은 PayMethodInfo<Card>와 PayMethodInfo<Bank>의 유니온 타입이므로, 이 함수가 반환하는 데이터는 PayMethodInfo<Card>와 PayMethodInfo<Bank>의 배열이다. 그러나 필터링을 통해 반환된 데이터는 PocketInfo<Card> \| PocketInfo<Bank> 타입의 요소를 포함하게 된다.
- 따라서, **최종적으로 useGetRegisteredList 함수가 반환하는 데이터의 타입은 PocketInfo<Card>[] | PocketInfo<Bank>[] 요소를 포함할 수 있는 UseQueryResult<PayMethodType[]>이다.**
- **인자로 넣는 타입에 알맞은 타입을 반환하고 싶지만, 타입설정이 유니온으로만 되어있기 때문에 타입스크립트는 해당 타입에 맞는 Data 타입을 추론할 수없다.** 이처럼 인자에 따라 반환되는 타입을 다르게 설정하고 싶다면 extends를 사용한 조건부 타입을 활용하면 된다.

#### 5.1.3 extends 조건부 타입을 활용하여 개선하기

- useGetRegisteredList 함수의 반환 Data는 인자 타입에 따라 정해져 있다. 타입으로 "card" 또는 "appcard"를 받으면 카드 결제 수단 정보 타입인 Pocketlnfo<card>를 반환하고, "bank"를 받는다면 Pocketlnfo<bank>를 반환한다.
    - type: "card" | "appcard => PocketInfo<Card>
    - type: "bank" => PocketInfo<Bank>

- 이러한 상황에서 조건부 타입을 활용하면 하나의 API 함수에서 타입에 따른 정확한 반환 타입을 추론하게 만들 수 있다.
- 조건부 타입을 사용해서 PayMethodInfo<Card> | PayMethodInfo<Bank> 타입이었던 PayMethodType 타입을 개선해보자.

```js
type PayMethodType<T extends "card" | "appcard" | "bank"> = T extends
| "card"
| "appcard"
? Card
: Bank;
```

- PayMethodType의 제네릭으로 받은 값이 "card" 또는 "appcard"일 때는 PayMethodlnfo<Card> 타입을, 아닐 때는 PayMethodInfo<Bank> 타입을 반환하도록 수정했다. 또한 결제 수단 타입에는 "card", "appcard". "bank"만 들어올 수 있기 때문에 **extends를 한정자로 활용해서 제네릭에 넘 겨오는 값을 제한**하도록 했다.
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

- UnpackPromise 타입은 제네릭으로 T를 받아 **T가 Promise로 래핑된 경우라면 K를 반환하고,그렇지 않은 경우에는 any를 반환**한다. **Promise<infer K>는 Promise의 반환 값을 추론해 해당 값의 타입을 K로 한다는 의미**이다.
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

- MainMenu와 SiibMenu는 메뉴 리스트에서 사용하는 타입으로 권한 API를 통해 반환된 사용자 권한과 name을 비교하여 사용자가 접근할 수 있는 메뉴만 렌더링한다. MainMenu의 name은 subMenus를 가지고 있을 때 단순히 이름의 역할만 하며, 그렇지 않을 때는 권한으로 간주된다.
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
- T extends ReadonlyArray<infer U>
    - T가 ReadonlyArray 타입인 경우, U는 배열의 요소 타입
    - 예를 들어, T가 ReadonlyArray<MainMenu \| SubMenu>라면 U는 MainMenu | SubMenu 타입
- U extends MainMenu
    - U가 MainMenu 타입인 경우를 처리
    - MainMenu는 선택적 subMenus 속성을 포함할 수 있음
- U["subMenus"] extends infer V
    - U의 subMenus 속성을 V 타입으로 추론
    - V는 ReadonlyArray<SubMenu> 타입
- V extends ReadonlyArray<SubMenu>
    - V가 ReadonlyArray<SubMenu> 타입인 경우를 처리
    - V가 ReadonlyArray<SubMenu>라면 UnpackMenuNames<V>를 재귀적으로 호출
    - 재귀 호출을 통해 SubMenu의 이름을 추출
- : U["name"]
    - V가 ReadonlyArray<SubMenu>가 아닌 경우, U["name"]을 반환
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


<h3>끝!</h3>
