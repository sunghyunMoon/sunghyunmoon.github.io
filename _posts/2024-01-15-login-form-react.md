---
title: React로 로그인 form 만들기
categories:
- ToyProject
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

이번에는 React를 이용해 로그인 form을 만들어보자.

<div><img src= "/assets/img/post/login_form.PNG"></div>

### 컴포넌트 구조 설계

- 이름, 비밀번호, 비밀번호 확인 Input Control을 담당하는 FormInput 컴포넌트가 있고, 이러한 FormInput 컴포넌트로 구성된 Form 컴포넌트가 있다고 하자.
- 그리고 From 컴포넌트와 Sibling으로 fontSize를 제어하는 FontControlBox 컴포넌트를 구성하자.
- 그리고 가입하기를 누르면 아이디, 비밀번호를 확인하는 모달인 Modal 컴포넌트도 구성하자.

### state 관리 설계

- 최상위 App 컴포넌트의 자식으로 Form 컴포넌트와 Modal 컴포넌트가 있다. 우선 **Model 컴포넌트가 렌더링되기 위해서는 id, 비밀번호, 비밀번호 확인 상태값이 필요**하다.
- 그리고 **Form 컴포넌트의 가입하기(submit) 버튼을 눌렀을 때, id와 비밀번호, 그리고 비밀번호 확인하는 과정이 필요**하다. 즉, id와 비밀번호, 비밀번호 확인 상태값들이 필요하다.
- id와 비밀번호의 값은 Form 컴포넌트의 자식인 FormInput 컴포넌트에서 event로 받는다. 이 값들에 따라서 FormInput 컴포넌트의 에러 메세지가 렌더되기도하고, Form 컴포넌트의 가입하기 버튼을 눌렀을때 Model 컴포넌트 렌더 여부를 체크하기도하고, Modal 컴포넌트에서 이 값들을 렌더해준다.
- 따라서, id, 비밀번호, 비밀번호 확인 상태 값들은 global state로 관리해야한다. Form과 Modal의 부모인 App 컴포넌트에서 관리하면 적절할 것 같다.

#### Context 초기 설정

- 아래 코드에서 **FormContext는 React Context API를 사용해 전역적으로 상태를 관리하기 위해 만들었다. formData라는 상태와 setFormData라는 상태 업데이트 함수를 공유하여, 여러 컴포넌트에서 같은 폼 데이터에 접근하고 업데이트 할 수 있도록 만들었다.**

```js
const initialFormData = {
    id: '',
    pw: '',
    confirmPw: '',
}

export const FormContext = createContext({
    formData: initialFormData,
    setFormData: () => {},
})

function App() {
    const [formData, setFormData] = useState(initialFormData)

    return (
        <FormContext.Provider value={{ formData, setFormData }}>
            <section className="form-wrapper">
                <Form />
            </section>
            <FontControlBox />
            <Modal />
        </FormContext.Provider>
    )
}
```

- FormContext는 formData와 setFormData를 포함하는 컨텍스트다. 이렇게 하면 FormContext를 사용하는 컴포넌트는 formData와 setFormData에 접근할 수 있다. setFormData는 기본적으로 빈 함수로 설정되고, 실제로는 FormContext.Provider에서 setFormData를 전달하게 된다.

#### Form 컴포넌트에서의 errorData state 관리

- Form 컴포넌트에서는 가입하기(submit) 버튼을 누르면, 각 FormInput 컴포넌트의 input 값들이 유효한지 알아야한다. 그리고 FormInput 컴포넌트에서는 id에 따라 유효하지 않은 경우 각각 다른 에러 메세지를 띄운다.
- 각 FormInput 컴포넌트의 유효성에 따른 상태값을 Form 컴포넌트에서 관리해 자식인 FormInput 컴포넌트에 props로 넘겨줘야 한다.

```js
const initialErrorData = {
    id: '', // true or invalidId
    pw: '', // true or invalidPw
    confirmPw: '', // true or invalidConfirmPw
}

const Form = ({ modalRef }) => {
     const [errorData, setErrorData] = useState(initialErrorData)
     const handleSubmit = (e) => {
        e.preventDefault()
        const isValid = Object.values(errorData).every((val) => val === true)
        if (isValid === true) modalRef.current.showModal()
    }

     return (
        <FormInput
                id={'id'}
                label={'아이디'}
                inputProps={{
                    type: 'text',
                    placeholder: '아이디를 입력해주세요.',
                    autoFocus: true,
                }}
                errorData={errorData}
                setErrorData={setErrorData}
        />
     )
}
```

- 따라서, 각 FormInput에 따른 에러 상태를 나타내기 위해 initialErrorData를 만들었고, Form 컴포넌트의 errorData의 초기값으로 설정한다.
- errorData 객체의 value 값을 순회해 모두 true인 경우에만 모달 컴포넌트의 ref DialogElement를 열도록 한다.
- 그리고 errorData와 상태 업데이트 함수인 setErrorData를 FormInput 컴포넌트의 props로 넘겼다.

#### FormInput 컴포넌트에서의 유효성 검사와 에러 메세지 렌더

- FormInput 컴포넌트의 props는 id, label, errorData, setErrorData가 있다. 그리고 type과 placeholder는 하나의 inputProps 객체로 묶어서 전달했다.
- 아래와 같이 Form 컴포넌트의 state인 errorData 객체의 value 값들에 대해 에러 메세지를 맵핑하고자 ERROR_MSG 객체를 만들었다. 이를 통해 가독성과 유지보수성, 그리고 확장성을 향상시킬 수 있다.

```js
const ERROR_MSG = {
    required: '필수 정보입니다.',
    invalidId:
        '5~20자의 영문 소문자, 숫자와 특수기호(_),(-)만 사용 가능합니다.',
    invalidPw: '8~16자 영문 대 소문자, 숫자를 사용하세요.',
    invalidConfirmPw: '비밀번호가 일치하지 않습니다.',
}
```

- 그리고 onBlur 이벤트에서 checkRegex 이벤트 핸들러를 호출해 useContext 훅을 통해 받은 formData의 input 값들에 대해 유효성 검사를 수행한다.

```js
const checkRegex = (inputId) => {
        let result
        const value = formData[inputId]
        if (value.length === 0) {
            result = 'required'
        } else {
            switch (inputId) {
                case 'id':
                    result = ID_REGEX.test(value) ? true : 'invalidId'
                    break
                case 'pw':
                    result = PW_REGEX.test(value) ? true : 'invalidPw'
                    checkRegex('confirmPw')
                    break
                case 'confirmPw':
                    result =
                        value === formData['pw'] ? true : 'invalidConfirmPw'
                    break
                default:
                    return
            }
        }
        // 정리!
        setErrorData((prev) => ({ ...prev, [inputId]: result }))
}

<input
    id={id}
    {...inputProps}
    value={formData[id]}
    onChange={(e) =>
        setFormData({ ...formData, [id]: e.target.value })
    }
    onBlur={() => checkRegex(id)}
/>
```

- 위와 같이 기존 errorData 객체에서 해당 id에 대한 값만 유효성 검사의 결과 값으로 변경해 state를 업데이트한다.

```js
<div id="id-msg" className="mt-1 mb-3 text-xs text-red-500">
    {errorData[id] !== true ? ERROR_MSG[errorData[id]] : ''}
</div>
```

- FormInput 컴포넌트에서 errorData의 값이 true가 아닌 경우는 ERROR_MSG를 렌더한다.


### Modal 컴포넌트

- 위 Form 컴포넌트의 가입하기 버튼을 눌렀을때, errorData 객체의 값들이 모두 true인 경우 Modal 컴포넌트를 렌더했다.
- 여기서 Form 컴포넌트에서 Modal 컴포넌트의 특정 DOM에 대한 접근이 필요하다. 따라서 forwardRef를 사용해 Modal 컴포넌트의 ref를 부모인 App 컴포넌트에 공유하고, App 컴포넌트는 공유 받은 ref를 자식인 Form 컴포넌트에 props로 전달했다.

### FontControlBox 컴포넌트

- FontControlBox 컴포넌트는 아래와 같다. 

```js
const $html = document.documentElement
const MAX_FONT_SIZE = 20
const MIN_FONT_SIZE = 12

const getHtmlFontSize = () => {
    return parseFloat(window.getComputedStyle($html).fontSize)
}

const FontControlBox = () => {
    const [fontSize, setFontSize] = useState(getHtmlFontSize())

    const onClickFontsizeControl = (flag) => {
        if (flag === 'increase') {
            setFontSize((prev) => prev + 1)
        } else {
            setFontSize((prev) => prev - 1)
        }
    }

    useLayoutEffect(() => {
        $html.style.fontSize = fontSize + 'px'
    }, [fontSize])

    return (
        <aside id="font-control-box" className="flex fixed bottom-0 right-0">
            <button
                id="increase-font-btn"
                onClick={() => onClickFontsizeControl('increase')}
                disabled={fontSize >= MAX_FONT_SIZE}
                className="bg-white text-gray-500 border border-gray-300 hover:bg-red-50 focus:outline-none focus:shadow-outline disabled:bg-gray-500 disabled:text-white rounded-full"
            >
                +
            </button>
            <button
                id="decrease-font-btn"
                onClick={() => onClickFontsizeControl('decrease')}
                disabled={fontSize <= MIN_FONT_SIZE}
                className="bg-white text-gray-500 border border-gray-300 hover:bg-blue-50 focus:outline-none focus:shadow-outline disabled:bg-gray-500 disabled:text-white rounded-full"
            >
                -
            </button>
        </aside>
    )
}
```

- $html은 html 태그를 의미하고, 폰트 크기를 직접 변경하여 페이지 전체의 기본 폰트 크기를 조정하는 역할한다.
- fontSize 상태는 현재 html 요소의 폰트 크기를 저장한다. 초기값으로 getHtmlFontSize()의 반환값을 사용해 현재 폰트 크기를 설정했다.
- fontSize가 변경될때 마다 useLayoutEffect의 콜백 함수가 실행되고, html 요소의 fontSize 스타일을 fontSize 상태 값으로 업데이트한다.

### 완성본

<a href="https://sunghyunmoon.github.io/signup-form/src/">
완성본 보러가기
</a>

<h3>끝!</h3>
