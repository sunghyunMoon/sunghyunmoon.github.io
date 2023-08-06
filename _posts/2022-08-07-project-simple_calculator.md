---
title: 계산기 만들기
categories:
- Toy Project
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

 

#### HTML/CSS 구현

- html head

```html
<head>
    <meta charset='utf-8'>
    <meta http-equiv='X-UA-Compatible' content='IE=edge'>
    <title>Simple Calculator</title>
    <meta name='viewport' content='width=device-width, initial-scale=1'>
    <link rel='stylesheet' type='text/css' media='screen' href='main.css'>
    <script src='main.js'></script>
</head>
```

- 간단하게 root 디렉토리에 index.html과 main.css로 구성했다. 그리고 live server를 구동해 html 수정시 즉각적으로 브라우저에서 확인 가능하도록 했다. 

<div><img src= "/assets/img/post/calculator.PNG"></div>

```css
.btn {
    width: 48px;
    height: 48px;
    background-color: #2699fb;
    color: white;
    border-radius: 4px;
    outline: none;
    border: none;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
    font-weight: bold;
}

.btn:active {
    background-color: #007feb;
}
```

- btn 내의 텍스트들을 가운데 정렬하기 위해 display를 flex로 설정해 justify-content를 center로 설정했다. 

```html
<div class="row">
        <input class="inp" disabled id="top-inp">
    </div>
    <div class="row">
        <div class="btn" onclick="inputNum(7)">7</div>
        <div class="btn" onclick="inputNum(8)">8</div>
        <div class="btn" onclick="inputNum(9)">9</div>
        <div class="btn" onclick="inputEqual()">=</div>
    </div>
    <div class="row">
        <div class="btn" onclick="inputNum(4)">4</div>
        <div class="btn" onclick="inputNum(5)">5</div>
        <div class="btn" onclick="inputNum(6)">6</div>
        <div class="btn" onclick="inputOper('+')">+</div>
    </div>
    <div class="row">
        <div class="btn" onclick="inputNum(1)">1</div>
        <div class="btn" onclick="inputNum(2)">2</div>
        <div class="btn" onclick="inputNum(3)">3</div>
        <div class="btn" onclick="inputOper('-')">-</div>
    </div>
    <div class="row">
        <div class="btn btn-lg" onclick="inputNum(0)">0</div>
        <div class="btn" onclick="inputOper('/')">/</div>
        <div class="btn" onclick="inputOper('*')">*</div>
    </div>
```

```css
.row {
    display: flex;
    gap: 4px;
    margin-bottom: 4px;
}
```

- row element에 flex를줘 btn element들이 수평 레이아웃 되도록 했다. 그리고 gap을 통해 row의 flex item들이 4px 만큼의 gap을 갖도록 했다. 그리고 다음 row들과의 gap을 주기 위해 margin-bottom을 이용했다. 

```css
.inp {
    width: 204px;
    height: 48px;
    box-sizing: border-box;
    outline: none;
    color: #bce0fd;
    padding: 0 20px;
}
 
.inp:disabled {
    background-color: white;
    border: solid 1px #bce0fd;
    font-size: 14px;
}
```

- **input 요소의 크기를 box-sizing: border-box를 이용해 콘텐츠 영역, 여백, 테두리까지 포함되도록 했다.**
- 그리고 버튼 클릭 기반으로만 간단한 계산기를 구현할 것이기 때문에, input 요소는 클릭이 되거나 입력이 되면 안된다. 그래서 disabled 속성을 줬다.

#### main.js 구현

- 변수
  - left : operator 왼쪽 숫자
  - operator : +, -, *, /
  - right : operator 오른쪽 숫자
  - equal : equal이 클릭됐는지 불린 값
  - res : input 필드에 표현되는 결과 값

##### 클릭 이벤트를 통한 숫자 입력 함수

- inputNum 메서드

```js
function inputNum(num) {
    if (oper === null) {
        if (left === null) {
            left = `${num}`
        } else {
            if (num === 0 && parseInt(left) === 0)  return;
            left += `${num}`
        }
    } else {        
        if (right === null) {
            right = `${num}`
        } else {
            right += `${num}`
        }
    }
    console.log(left);
    save();
}
```

- operator가 안들어온 상태에서, left도 안들어왔으면 left값을 새롭게 설정하고, left 값이 들어왔으면 string을 추가해준다.
- operator가 들어온 상태에서, right이 안들어왔으면 right을 새롭게 설정하고, right 값이 들어왔으면 string을 추가해준다.

##### operator를 받는 함수

- inputOper 메서드

```js
function inputOper(op) {
    if (left === null && op === "-") {
        left = '-';
        save();
        return;
    }
    if (left === "-" && op === "-")   return;
    if (op === "-" && oper !== null && right === null) {
        right = "-";
        save();
        return;
    }
    
    oper = op;
    save();
}
```

- 첫 값이 - 일수 있기 때문에, left가 null이고 -값이 들어오면 left를 -로 설정한다. 그리고 -가 또 들어오면 early return
- 그리고 right 값이 -일 수 있기 때문에 그에 대한 처리를 했다.

##### equal를 받는 함수

- inputEqual 메서드

```js
function inputEqual() {
    if (res) {
        left = res;
        right = null;
        res = null;
        oper = null;
        equal = false;
    }
    equal = true;
    save(); 
}
```

- 기본적으로 equal 불린 값을 true로 설정한다.
- eqaul이 true인 상태에서 또 equal을 클릭하면 기존 res를 left로 설정하고 모두 초기화를 해준다.

##### save 함수

- left, right, operator 값이 존재할때 input 필드에 적용시켜주는 메서드

```js
function save() {
    const inp = document.getElementById("top-inp");
    let value = "";
    
    if (left === null)  return;
    value += left + " ";
    inp.value = value;
    if (oper === null)  return;
    value += oper + " ";
    inp.value = value;
    if (right === null)  return;
    value += right + " ";
    inp.value = value;

    if (equal) {
        switch (oper) {
            case "+":
                res = parseInt(left) + parseInt(right);
                break;
            case "-":
                res = parseInt(left) - parseInt(right);
                break;
            case "*":
                res = parseInt(left) * parseInt(right);
                break;
            case "/":
                res = parseInt(left) / parseInt(right);
                break;
        }
        value += "= " + res;
        inp.value = value;
    }
}
```

- left, oper, right 순서대로 null check를 하고 input 요소에 value를 설정해준다.

- equal이 true인 경우 실제 연산을 통해 input 요소의 value에 설정을 해준다.


<a href="https://sunghyunmoon.github.io/calculator">완성본 보러가기</a>

<h3>끝!</h3> 
