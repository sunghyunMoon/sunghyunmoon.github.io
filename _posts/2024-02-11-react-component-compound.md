---
title: React Compound Component Pattern
categories:
- React
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

React Compound Component Pattern에 대해 공부해보자.

### 리액트 컴파운드 컴포넌트 패턴 소개

- 리액트(React)를 사용하다 보면, **한 컴포넌트 내에서 특정 기능을 수행하고, 그 기능과 밀접하게 연관된 하위 요소들을 함께 구성하는 상황**을 자주 맞닥뜨리게 된다. 
- 이때 흔히 떠오르는 것은 "props drilling" 혹은 하위 컴포넌트에 일일이 필요한 값을 내려주는 패턴이다. 하지만 이런 방법은 컴포넌트 구조가 커지면서 점점 복잡해지고, 재사용성이나 가독성이 떨어질 수 있다. 이러한 문제를 해결하기 위한 한 가지 유용한 디자인 패턴이 바로 컴파운드(Compound) 컴포넌트 패턴이다.

### w/o 컴파운드 컴포넌트 패턴 리액트 코드

- 컴파운드 컴포넌트 패턴 없이 아래와 같은 간단한 드롭다운(셀렉트 박스) UI를 만들어 보자.

<div><img src= "/assets/img/post/compound_dropdown.PNG"></div>

- 우선 전체 드롭다운 로직을 관리하는 최상위 컴포넌트인 Select 컴포넌트가 있다. Select에서 상태를 모두 관리하고, 그 상태를 Trigger, Menu, Option 컴포넌트에 props로 하나하나 전달하는 형태다.
- 그리고 현재 선택된 값을 보여주고, 클릭 시 메뉴를 열거나 닫아주는 버튼인 Trigger 컴포넌트가 있다. 
- Menu 컴포넌트에서는 실제 드롭다운 목록을 보여준다.
- Option 컴포넌트는 개별 옵션 항목을 나타낸다.

```js
function App() {
    const options = [
        { value: 'apple', label: 'Apple' },
        { value: 'banana', label: 'Banana' },
        { value: 'orange', label: 'Orange' },
    ];
    return (
        <div className="App">
            <Select options={options} />
        </div>
    );
}

const Select = ({ options = [] }) => {
    // 상태를 이용해 메뉴가 열렸는지 닫혔는지 관리
    const [isOpen, setIsOpen] = useState(false);
    // 현재 어떤 옵션이 선택되었는지 저장
    const [selectedValue, setSelectedValue] = useState(null);

    // 메뉴 열림/닫힘을 제어
    const toggle = () => setIsOpen(!isOpen);
    // 옵션 선택 시 상태를 갱신하고 메뉴를 닫음
    const selectOption = (value) => {
        setSelectedValue(value);
        setIsOpen(false);
    };

    return (
        <div
            className="select-container"
        >
            <Trigger
                isOpen={isOpen}
                selectedValue={selectedValue}
                onToggle={toggle}
            />
            <Menu isOpen={isOpen}>
                {options.map((option) => (
                    <Option
                        key={option.value}
                        value={option.value}
                        onSelect={selectOption}
                    >
                        {option.label}
                    </Option>
                ))}
            </Menu>
        </div>
    );
};

export default Select;
```

- 아래는 Trigger 컴포넌트다. Select 컴포넌트로부터 isOpen, selectedValue, onToggle를 props로 받는다.
- isOpen에 따라 화살표 아이콘을 위(▲)나 아래(▼)로 바꾼다.
- onClick 이벤트에 onToggle 이벤트 핸들러를 등록해 isOpen 상태를 변경한다.

```js
const Trigger = ({ isOpen, selectedValue, onToggle }) => {
    return (
        <button
            onClick={onToggle}
        >
            {selectedValue ?? 'Select an option'} {isOpen ? '▲' : '▼'}
        </button>
    );
};
```

- 그리고 아래는 Menu와 Option 컴포넌트다.

```js
const Menu = ({ isOpen, children }) => {
    if (!isOpen) return null;
    return (
        <ul>
            {children}
        </ul>
    );
};

const Option = ({ value, onSelect, children }) => {
    return (
        <li
            onClick={() => onSelect(value)}
        >
            {children}
        </li>
    );
};
```

- Menu 컴포넌트는 Select로부터 isOpen을 props로 받아서 true일 때만 목록을 표시해준다.

### w/o 컴파운드 컴포넌트 패턴 문제점

- 위 구조는 Select 컴포넌트 내부에서 모든 로직과 데이터 흐름을 관리하면서 하위 컴포넌트에 props를 직접 전달하고 있는 형태다. 이로 인해 하위 컴포넌트들을 자유롭게 배치하거나 추가적인 UI 변화를 주려면 Select 컴포넌트를 계속 수정해야 되고, props 전달이 더 많아 질 수 있다.
- 이렇게 컴포넌트 패밀리가 명확히 정의 되지 않으면, 나중에 코드 구조를 파악하거나 수정할 때 "이 컴포넌트는 어디에 속하고 어떻게 함께 사용되는지" 한번 더 고민해야 하며, **각각의 컴포넌트를 독립적으로 이해하고 관리**해야 한다.
- 다시 말해, 기능은 단순하지만, 확장성과 유연성, 그리고 유지 보수성이 떨어지는 구조라고 볼 수 있다.

### w/ 컴파운드 컴포넌트 패턴 리액트 코드

- 아래는 컴파운드 컴포넌트 패턴을 적용한 App과 Select 컴포넌트다.

```js
function App() {
    return (
        <div className="App">
            <Select>
                <Select.Trigger />
                <Select.Menu>
                    <Select.Option value="apple">Apple</Select.Option>
                    <Select.Option value="banana">Banana</Select.Option>
                    <Select.Option value="orange">Orange</Select.Option>
                </Select.Menu>
            </Select>
        </div>
    );
}

const SelectContext = createContext();

export function useSelectContext() {
    const context = useContext(SelectContext);
    if (!context) {
        throw new Error(
            'Select compound components must be used within a Select'
        );
    }
    return context;
}

const Select = ({ children }) => {
    const [isOpen, setIsOpen] = useState(false); // 메뉴 열림/닫힘 상태
    const [selectedValue, setSelectedValue] = useState(null); // 선택된 옵션 값

    const toggle = () => setIsOpen((prev) => !prev); // 메뉴 열림/닫힘 토글 함수
    const selectOption = (value) => {
        setSelectedValue(value); // 새로운 값 선택
        setIsOpen(false); // 선택 후 메뉴 닫기
    };

    const value = { isOpen, selectedValue, toggle, selectOption };

    return (
        <SelectContext.Provider value={value}>
            <div
            >
                {children}
            </div>
        </SelectContext.Provider>
    );
};

export default Select;

Select.Trigger = Trigger;
Select.Menu = Menu;
Select.Option = Option;
```

- 컴파운드 컴포넌트 패턴을 사용한 Select 컴포넌트는 하나의 상위 컴포넌트(Select)와 그 안에서만 의미있게 동작하는 하위 컴포넌트들(Select.Trigger, Select.Menu, Select.Option)을 하나의 세트로 묶는다.
- **App 컴포넌트를 보면 하위 컴포넌트인 Select.Trigger, Select.Menu, Select.Option을 별도로 import하지 않고, Select 하나만 import한 뒤 Select 객체 안에 있는 하위 컴포넌트들을 점(.)을 통해 바로 접근**한다.
- 상위 컴포넌트(Select) 안에서 이미 Context를 통해 상태가 공유되고 있으므로, 하위 컴포넌트는 복잡한 props 없이 상위 상태를 활용한다.
- 컴포넌트를 재배치하거나 하위 컴포넌트를 추가할 때도 **코드 구조가 깔끔하고 직관적**이다.

```js
const Trigger = () => {
    const { isOpen, toggle, selectedValue } = useSelectContext();
    return (
        <button
            onClick={toggle}
        >
            {selectedValue ?? 'Select an option'}{' '}
            <span>{isOpen ? '▲' : '▼'}</span>{' '}
        </button>
    );
};

const Menu = ({ children }) => {
    const { isOpen } = useSelectContext();
    return isOpen ? (
        <ul>
            {children}
        </ul>
    ) : null; // isOpen이 true일 때만 목록 표시
};

const Option = ({ value, children }) => {
    const { selectOption } = useSelectContext();
    return (
        <li
            onClick={() => selectOption(value)}
        >
            {children}
        </li>
    );
};
```

### w/ 컴파운드 컴포넌트 패턴의 장점

- App 컴포넌트를 비교해봤을때 w/o 컴파운드 컴포넌트 패턴의 경우, options를 전달하기 위해 props drilling이 필요하지만, w/ 컴파운드의 경우 하위 컴포넌트에 곧바로 props를 전달할 수 있다.
- 또한 Context API를 사용하면서 props drilling을 줄였다. 이를 통해 **상위 컴포넌트만 한 번 import한 뒤, 그 안에 있는 하위 컴포넌트를 바로 호출해서 사용하는 구조로 자연스럽게 형성**할 수 있었다.
- 따라서 코드적으로 상위 컴포넌트와 그와 밀접하게 관련된 하위 컴포넌트들이 한데 묶여 있으므로, 이들이 "한 세트"라는 사실이 명확해진다. "이 하위 컴포넌트들은 이 상위 컴포넌트와 함께 써야 하는구나"라는 점을 코드 구조에서 바로 느낄 수 있다.

### 정리

- **컴파운트 컴포넌트은 상위 컴포넌트가 상태와 로직을 관리하고, 하위 컴포넌트들은 이 상위 컴포넌트에서 제공하는 Context를 통해 해당 상태와 로직에 접근**한다.
- 그리고 **상위 컴포넌트만 import한 뒤, 그 내부에 배치된 하위 컴포넌트들을 자유롭게 조합하여 하나의 기능 단위(Component family)로 사용하는 패턴**이다.
- 즉, 상위 컴포넌트와 하위 컴포넌트 사이에 명확한 관계와 하나의 패키지로서의 응집성을 강조하는 것이 컴파운트 패턴이다.

<h3>끝!</h3>
