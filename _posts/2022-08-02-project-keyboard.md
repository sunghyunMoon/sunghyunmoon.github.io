---
title: 가상 keyboard 만들기
categories:
- Toy Project
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

첫 프로젝트로 가상 키보드를 만들어 보자. 웹팩을 활용해서 개발환경을 설정하고, HTML과 CSS를 이용해 키보드 레이아웃을 하고, 다크 테마 기능과 폰트 변경 기능도 추가 해보자. 그리고 마지막으로 키 이벤트를 이용해 실제 키보드를 눌렀을 때 어떤 키가 눌러지는지 화면상으로 보여주도록 하자. 그리고 마우스 이벤트를 이용해서도 키 컨트롤을 눌렀을 때 입력이 가능하도록 하자. 

#### 웹팩 설정

- 가장 먼저, package.json을 초기화

```
npm init –y
```

- 그 다음, 웹팩 관련 패키지를 설치하자.

```
npm i –D webpack webpack-cli webpack-dev-server
```

- webpack.config.js 설정

```js
module.export = {
    entry: "./src/js/index.js",
    output: {
        filename: "bundle.js",
        path: path.resolve(__dirname, "./docs"),
        clean: true
    },
    devtool: "source-map",
    mode: "development",
    devServer: {
        host: "localhost",
        port: 8080,
        open: true,
        watchFiles: 'index.html'
    },
    plugins:[
        new HtmlWebpackPlugin({
            title: "keyboard",
            template: "./index.html",
            inject: "body",
            favicon: "./favicon.PNG"
        }),
        new MiniCssExtractPlugin({
            filename:"style.css"
        })
    ],
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, "css-loader"]
            }
          ]
    },
    optimization: {
        minimizer: [
            new TerserWebpackPlugin(),
            new CssMinimizerPlugin()
        ]
    }
}
```

- entry는 index.js로, 번들링 결과인 output을 bundle.js로 설정했다. path 같은 경우 git으로 올리면 자동으로 /docs의 index.js로 접근하기 때문에 docs로 설정했다. 
- 상대 경로는 웹팩에서 경로를 찾을 수 없기 때문에, path 모듈의 resolve 메서드를 이용해서 웹팩이 절대 경로를 찾을 수 있도록 했다. 
- devtool 속성으로 source-map을 사용했다. **source-map은 원본 소스코드와 변환된 번들 소스 코드 같의 매핑 정보를 포함하는 파일**이다. 번들링된 결과물을 여러 개의 소스 파일들을 하나의 파일로 결합했기 때문에 디버깅하기 어렵다. source-map은 번들링된 파일과 원본 소스 파일 사이의 매핑 정보를 제공하여 디버깅 시 원본 소스 코드에서 문제를 식별하고 수정할 수 있게 도와준다.
- css와 htlm 파일들을 설정해줄 모듈들을 설치한다. 

```
npm i -D html-webpack-plugin
npm i -D mini-css-extract-plugin css-loader css-minimizer-webpack-plugin
```
- **HtmlWebpackPlugin**은 번들링된 JS 파일을 HTML에 자동으로 삽입하고, 필요한 CSS 파일을 추가하는 등의 작업을 수행해 개발자가 HTML 파일을 관리하기 쉽게 도와준다. inject은 번들 파일이 삽입될 위치를 말한다.('head' 또는 'body') 그리고 template을 index.html로 설정하면 HtmlWebpackPlugin이 template 파일 안에서 lodash 문법을 사용할 수 있도록 해준다. 
- **MiniCssExtractPlugin**은 웹팩 번들링 과정에서 CSS 코드를 별도의 파일로 추출하여 로드할 수 있도록 도와준다.
- module.reules 속성을 이용해 웹팩이 css 파일을 로드하고 변환하도록 해준다.
- **TerserWebpackPlugin을 이용해 JS 코드를 압축**하도록 했다. 그리고 **CssMinimizerPlugin을 이용해 CSS 파일을 압축**하도록 했다. 
- webpack-dev-server를 이용해 웹팩으로 개발한 애플리케이션을 로컬 개발 환경에서 실시간으로 확인하도록 했다. watchFiles 속성을 이용해 index.html 변화가 있을 때 마다 리로드하도록 했다. 

#### ESLint & Prittier

- eslint & prettier 설치

```
npm i -D eslint
npm i -D --save-exact prettier
```

- package.json에 ^가 있는 경우는, 버전을 지정해서 설치하지 않은 경우다. ^은 패키지가 업데이트 되었을 때, minor 버전에 한해서 업데이트를 허용한다는 의미다. 그래서 --save-eact 옵션을 이용하면 ^이 없어서 패키지가 업데이트되더라도 버전이 바뀌지 않는다. 버전에 따라서 prettier 설정이 달라질 수 있기 때문에 버전을 fix해 놓는다. 

```
npm i -D eslint-config-prettier eslint-plugin-prettier
```

- eslint가 문법적인 오류 뿐만 아니라 포맷팅 기능도 제공한다. eslint는 문법의 오류를 체크하는 것이 main이고, prettier는 포맷팅이 main이다. eslint와 prettier가 포맷팅이 충돌나는 부분이 있다. eslint-config-prettier는 eslint에서 prettier와 포맷팅이 겹치는 rule들을 비활성화 해주는 역할을 한다. eslint-plugin-prettier는 eslint에 prettier의 포맷팅 rule들을 추가하는 패키지다. 

- eslint 설정 파일을 초기화하자.

```
npx eslint --init
```

- .eslintrc.json 생성

```js
{
    "extends": [
        "eslint:recommended",
        "plugin:react/recommended",
        "plugin:prettier/recommended"
    ],
}
```

- eslint에 prettier의 포맷팅 기능을 추가하기 위해 eslint 설정 파일의 extends에 위와 같이 추가했다. 

- .eslintignore 생성

```
/node_modules
/docs
webpack.config.js
```

- .eslintignore 파일에 eslint rule을 적용하고 싶지 않은 경로나 파일을 입력한다. 

- .prettierrc.json 생성

```
{
    "arrowParens": "always",
    "bracketSameLine": false,
    "bracketSpacing": true,
    "embeddedLanguageFormatting": "auto",
    "htmlWhitespaceSensitivity": "css",
    "insertPragma": false,
    "jsxSingleQuote": false,
    "printWidth": 80,
    "proseWrap": "preserve",
    "quoteProps": "as-needed",
    "requirePragma": false,
    "semi": true,
    "singleAttributePerLine": false,
    "singleQuote": false,
    "tabWidth": 2,
    "trailingComma": "es5",
    "useTabs": false,
    "vueIndentScriptAndStyle": false
  }
```

- prettier 기본 설정을 해볼텐데, https://prettier.io/playground/ 에 들어가서 기본적인 설정 JSON을 받아왔다. 
- .prettierignore은 .eslintignore와 같다.
- VS Code에서 ESLint 및 Prettier Extension을 설치하자. 
- Open Workspace Setting(JSON)

```
{
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
    }
}
```

- "editor.formatOnSave": true는 저장할때 포맷을 하게 해준다. Prettier는 JS, HTML, CSS에 대해 포맷팅이 가능하다. "source.fixAll.eslint": true는 저장할때 eslint rule대로 fix한다는 뜻이다. 

#### HTML

- index.html을 보자.
- 하나의 row 안에 key들이 나열되어 있다. row에 **display: flex;**를 이용해 키들을 표현했다.

```html
 <div class="row">
                <div class="key" data-code="Backquote" data-val="`">
                    <span class="two-value">~</span>
                    <span class="two-value">`</span>
                </div>
                <div class="key" data-code="Digit1" data-val="1">
                    <span class="two-value">!</span>
                    <span class="two-value">1</span>
                </div>
<div>
```

```css
.row {
    display: flex;
  }
```

- 기호 처럼 하나의 키에 위 아래로 두개를 입력 가능한 경우 해당 key에 대해 flex를 적용하고, **flex-wrap: wrap;**을 이용해 flex container가 아이템들을 한 줄에 담을 여유 공간이 없을때, 줄바꿈 처리를 해서 위/아래를 표현했다. align-items와 justify-content는 각각 수직/수평 축을 기준으로 가운데 정렬을 해준다. 

```css
.key {
    width: 60px;
    height: 60px;
    margin: 5px;
    border-radius: 4px;
    background-color: white;
    cursor: pointer;
    /* 하나의 키에 위 아래로 두개가 입력 가능한 경우 */
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
    transition: 0.2s;
}
```

- 체크 박스

```html
<label class="switch">
    <input id="switch" type="checkbox">
    <span class="slider"></span>
</label>
```

- **가상 요소 before를 이용해 slider를 표현**했다. 가사 요소를 사용하기 위해서는 content가 빈 string이라도 꼭 있어야 된다. 동그라미를 표현하기 위해 border-radius를 50%로 적용했다. 

```css
.slider::before {
    position: absolute;
    content: "";
    height: 26px;
    width: 26px;
    left: 4px;
    bottom: 4px;
    background-color: white;
    transition: 0.5s;
    border-radius: 50%;
}
```

- **input element인 switch의 checked 속성**을 이용해 slider를 transfrom시켰다. 

```css
input:checked + .slider::before {
    transform: translateX(26px);
}
```

#### Keyboard.js

- **Keyboard 클래스를 만들어서 DOM을 가져와서 event를 binding**했다. 
- **index.js에 Keyboard 인스턴스를 생성**한다.
- assignElement 메서드를 통해서 HTML element을 가져온다.

```js
#assignElement() {
    this.#switchEl = document.getElementById('switch');
    this.#fontSelectEl = document.getElementById('font');
    this.#keyboardEl = document.getElementById('keyboard');
    this.#inputGroupEl = document.getElementById('input-group');
    this.#inputEl = document.getElementById('input');
}
```

- addEvent 메서드를 통해 해당 DOM에 대해 event를 binding해줬다. 

```js
#addEvent() {
    this.#switchEl.addEventListener('change', this.#onChangeTheme);
    this.#fontSelectEl.addEventListener('change', this.#onChangeFont);
    this.#inputEl.addEventListener('input', this.#onInput.bind(this));
    document.addEventListener('keydown', this.#onKeyDown.bind(this));
    document.addEventListener('keyup', this.#onKeyUp.bind(this));
    this.#keyboardEl.addEventListener(
      'mousedown',
      this.#onMouseDown.bind(this)
    );
    document.addEventListener('mouseup', this.#onMouseUp.bind(this));
}
```

- switch의 경우 change 이벤트를 onChangeTheme 이벤트 핸들러에 binding했다. 그래서 event.target의 checked가 true이면 dark-mode 테마를 설정해주고, 아닌 경우는 설정하지 않도록 했다. 

```js
#onChangeTheme(ev) {
    document.documentElement.setAttribute(
      'theme',
      ev.target.checked ? 'dark-mode' : ''
    );
}
```

```css
html[theme="dark-mode"] {
    filter: invert(100%) hue-rotate(180deg);
  }
```

- html에 custom attribute로 theme를 설정해줬다. filter invert(100%)의 경우 색상이 반전된다.
- key가 입력되면 해당 키의 background에 변화를 줘, 해당 키가 입력되었다는 것을 알리도록 해봤다. 그렇게 하기 위해서는 **html 상 키보드를 통해서(custom attribute인 data-code) key를 찾아야하고, key event 객체의 code 속성의 값을 통해서 해당 key에 active라는 클래스를 추가해 background의 변화**를 줬다.

```css
.key.active {
    background-color: #333;
    color: #fff;
}
```

```js
#onKeyDown(ev) {
    if (this.#mouseDown) return;
    this.#keyPress = true;
    this.#keyboardEl
      .querySelector(`[data-code=${ev.code}]`)
      ?.classList.add('active');
}
  #onKeyUp(ev) {
    if (this.#mouseDown) return;
    this.#keyPress = false;
    this.#keyboardEl
      .querySelector(`[data-code=${ev.code}]`)
      ?.classList.remove('active');
}
```

- 한글 입력시 error 메세지를 띄우자.

```js
#onInput(ev) {
    if (this.#mouseDown) return;
    ev.target.value = ev.target.value.replace(/[ㄱ-ㅎ|ㅏ-ㅣ|가-힣]/, '');
    this.#inputGroupEl.classList.toggle(
      'error',
      /[ㄱ-ㅎ|ㅏ-ㅣ|가-힣]/.test(ev.data)
    );
}
```

- 우선 input.value 중 한글을 찾아서 빈 string으로 바꿨다.
- **classList에 toggle 메서드**를 이용해 event 객체의 data가 한글인 경우 classList에 error를 추가했다. 

- 키를 마우스로 눌렀을 때에도 input에 입력이 되도록 해보자.
- keyboard element 전체에 mouseDown/Up 이벤트를 바인딩하고, **mouseDown에서는 해당 key class에 대해 active를 설정**하고, **mouseUp에서는 active된 key element에 대해 custom attribute인 data-val 값을 inputElement의 value로 추가**해준다. 

```js
#onMouseDown(ev) {
    if (this.#keyPress) return;
    this.#mouseDown = true;
    ev.target.closest('div.key')?.classList.add('active');
}
#onMouseUp(ev) {
    if (this.#keyPress) return;
    this.#mouseDown = false;
    const keyEl = ev.target.closest('div.key');
    const isActive = !!keyEl?.classList.contains('active');
    const val = keyEl?.dataset.val;
    if (isActive && !!val && val !== 'Space' && val !== 'Backspace') {
      this.#inputEl.value += val;
    }
    // Space
    if (isActive && val === 'Space') {
      this.#inputEl.value += ' ';
    }
    // Backspace
    if (isActive && val === 'Backspace') {
      this.#inputEl.value = this.#inputEl.value.slice(0, -1);
    }
    this.#keyboardEl.querySelector('.active')?.classList.remove('active');
}
```

- mouseUp을 키보드 밖에서 했을 때에는 키 입력이 되지 않기 위해 mouseUp 이벤트는 document에 binding했다. 

```js
document.addEventListener('mouseup', this.#onMouseUp.bind(this));
```

<h3>끝!</h3>
