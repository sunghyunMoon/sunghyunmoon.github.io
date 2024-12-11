---
title: React로 장바구니 만들기
categories:
- ToyProject
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

이번에는 React JS를 이용해 장바구니를 만들어보자. 

### 컴포넌트 구성

- 컴포넌트는 크게 상품 리스트를 렌더하는 ProductList 컴포넌트와 오른쪽 툴페인인 CartToolpane 컴포넌트로 구성했다.
- ProductList는 productlist를 props로 받아 map을 통해 각 item들을 ProductItem 컴포넌트를 통해 렌더했다.

```js
const ProductList = ({ productList, handleProductItemClick }) => {
    if (productList === undefined || productList.length === 0) {
        return '상품이 없습니다.';
    }
    return (
        <>
            <section id="product-list">
                <div
                    id="product-card-grid"
                    className="grid gap-4 auto-cols-fr grid-cols-2 md:grid-cols-4"
                >
                    {productList?.map((item) => (
                        <ProductItem
                            key={item.id}
                            id={item.id}
                            imgSrc={item.imgSrc}
                            name={item.name}
                            price={item.price}
                            handleProductItemClick={handleProductItemClick}
                        />
                    ))}
                </div>
            </section>
        </>
    );
};
```

- 마찬가지로 CartToolpane 컴포넌트는 props로 cartlist를 CartList 컴포넌트에 전달하고, CartList 컴포넌트에서 CartItem 컴포넌트를 렌더했다.

### 비동기 API 요청 모킹하기

- src/api/productData.json 파일에 있는 상품 목록 데이터를 fetch API를 통해 비동기로 데이터를 받아와 렌더링했다. 

```js
useEffect(() => {
        const fetchProductData = async () => {
            const result = await getProductList();
            setProductList(result);
        };
        fetchProductData();
}, []);

const request = async (url) => {
    try {
        const response = await fetch(url);
        if (response.ok) {
            const data = await response.json();
            return data;
        }
        throw response.json();
    } catch (e) {
        console.log(e);
    }
};

const getProductList = async () => {
    const result = await request('./productData.json');
    return result;
};
```

- respone 객체의 json 메서드를 통해 JSON 형태를 파싱하여 자바스크립트 객체로 받았다.

### 상품 목록 렌더링하기

- fetch가 완료된 후 productList state를 초기화해 fetch된 productList를 ProductList 컴포넌트에 prpos로 전달해 초기화된 상품 목록을 렌더링하도록 했다.

### 장바구니 토글 기능

- 장바구니 아이콘을 클릭하거나 상품 목록 중 하나를 클릭하면 장바구니가 열려야한다.
- 장바구니 열린 상태에서 닫기 버튼을 누르거나 backdrop을 누르면 장바구니가 닫혀야 한다.

```js
<App>
    <ProductList />
    <CartList />
</App>
```

- 위 코드와 같이 ProductList와 CartList는 서로 다른 서브 트리 이지만, ProductList를 클릭하면 CartList가 열려야한다.
- 따라서 공통 부모인 App 컴포넌트에서 cartToolpaneOpen state를 토글하는 이벤트 핸들러를 구현했다.
- 아래의 이벤트 핸들러를 장바구니 아이콘, 상품 목록, 장바구니 닫기 버튼, backdrop의 onClick 이벤트 핸들러로 등록했다.

```js
const handleOpenCloseCartToolpane = () => {
        setCartToolpaneOpen((prev) => !prev);
};
```

### 장바구니 추가 기능

- 상품 카드를 클릭하면 상품이 장바구니에 추가되어야 한다.
- 따라서 App 컴포넌트에서 이벤트 핸들러를 만들어 ProductList 컴포넌트에 props로 전달했다.
- Productlist 컴포넌트는 ProductItem 컴포넌트로 전달하고, ProductItem의 onClick 이벤트 핸들러로 등록한다.

```js
 const handleProductItemClick = (e) => {
        // 장바구니 토글
        handleOpenCloseCartToolpane();
        const productId = e.target.dataset.productid;
        addCartItem(parseInt(productId));
};

    const addCartItem = (id) => {
        const selectedItem = productList.find((item) => item.id === id);
        const idx = cartList.findIndex((item) => item.id === id);
        if (idx === -1) {
            setCartList((prev) => [...prev, { ...selectedItem, count: 1 }]);
        } else {
            cartList[idx].count += 1;
            setCartList([...cartList]);
        }
};
```

- fetch한 productList의 정보는 App 커뮤포넌트에 있고, product 정보를 받으려면 id 정보가 필요하다. 따라서 ProductItem 컴포넌트의 HTMLElement의 dataset의 productid를 통해 id 정보를 받았다.
- 그리고 id 가있는 경우는 해당 cartItem의 count를 1 증가시키고, 없는 경우는 기존 cartList state에 새롭게 추가했다.

### 장바구니 상품 삭제 기능

- 장바구니 목록에서 삭제하기 글씨를 클릭하면 해당 상품이 삭제된다.

```js
const handleRemoveItem = () => {
        const newCratList = cartList.filter((item) => item.id !== id);
        setCartList([...newCratList]);
};
```

- 따라서 삭제하기 버튼에 handleRemoveItem 이벤트 핸들러를 구현해 등록했다.

### Web Storage API를 사용한 장바구니 데이터 저장 기능

- 장바구니의 결제하기 버튼을 누르면 장바구니 데이터를 브라우저에 저장하도록 했다.
- 따라서 새로고침을 하더라도 장바구니 데이터가 localStorage에 저장되어있으면 장바구니 데이터를 보여준다.

```js
// 장바구니 데이터 저장
const saveCartList = (e) => {
    e.preventDefault();
    localStorage.setItem('cartList', JSON.stringify(cartList));
};

// 장바구니 데이터 로드
useEffect(() => {
    setCartList(JSON.parse(localStorage.getItem('cartList')));
}, [setCartList]);
```

- localStorage 이용해 장바구니 데이터를 저장하고 로드했다.

### 완성본

<a href="https://sunghyunmoon.github.io/stopwatch-vanillajs/src/">
완성본 보러가기
</a>

<h3>끝!</h3>
