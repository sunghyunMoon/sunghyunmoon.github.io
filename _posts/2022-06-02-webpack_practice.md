---
title: 웹팩 실습
categories:
- Webpack
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

이전 포스트에서는 웹팩이 어떻게 등장했는지, 모듈 개념, 그리고 웹팩의 기본 개념에 대해 알아보았다. 이번 포스트에서는 웹팩을 실제 설치해보고 자주 사용하는 플러그인들에 대해 알아보고 추가해보자.

#### 1. webpack.config.js

- 프로젝트의 root 디렉토리에 웹팩 설정 파일인 webpack/config.js를 만들었다. 

##### 1.1 mode, entry, output 옵션

- mode, entry, output 옵션은 다음과 같다. 

```js
const path = require("path")

module.exports = {
  mode: "development",
  entry: "./src/js/index.js",
  output: {
    filename: "bundle.js",
        path: path.resolve(__dirname, "./dist"),
        clean: true
  },
}
```

- mode는 'development' 문자열을 사용했다.
- entry는 어플리케이션 진입점인 src/js/index.js로 설정한다.
- ouput은 번들링 결과인 bundle.js로 했다. 
    - output.path는 절대 경로를 사용하기 때문에 path 모듈의 resolve() 함수를 사용해서 계산했다. (path는 노드 코어 모듈 중 하나로 경로를 처리하는 기능을 제공한다)

- 웹팩 실행을 위한 NPM 커스텀 명령어를 추가했다.

package.json:

```
{
  "scripts": {
    "build": "webpack",
  }
}
```

- 모든 옵션을 웹팩 설정 파일로 옮겼기 때문에 단순히 webpack 명령어만 실행한다. npm run build로 웹팩 작업을 지시할 수 있다.

#### 2. plugins 옵션

- 플러그인은 webpack으로 변환한 파일에 추가적인 기능을 더하고 싶을 때 사용한다.(webpack의 기본적인 동작에 추가적인 기능을 제공)
- 로더가 파일 단위로 처리한다고 하면, **플러그인은 번들된 결과물을 처리**한다.
- loader와 plugin의 차이점?
    - loader는 파일을 해석하고 변환하는 과정에 관여하여 모듈을 처리
    - plugin은 해당 결과물의 형태를 바꾸는 역할을 하므로 번들링된 파일을 처리한다는 점
- 사용하고자 하는 플러그인을 웹팩 설정 객체의 plugins 배열에 설정한다. 클래스로 제공되는 플러그인의 생성자 함수를 실행해서 넘기는 방식이다.
- 개발하면서 플러그인을 직접 작성할 일은 거의 없다. 웹팩에서 직접 제공하는 플러그인을 사용하거나 써드파티 라이브러리를 찾아 사용하는데 자주 사용하는 플러그인에 대해 알아보자.

##### 2.1 BannerPlugin

- 결과물에 빌드 정보나 커밋 버전같은 걸 추가할 수 있다.

```js
const webpack = require('webpack');

module.exports = {
  plugins: [
    new webpack.BannerPlugin({
      banner: () => `빌드 날짜: ${new Date().toLocaleString()}`,
    })
  ]
```

##### 2.2 DefinePlugin

- 어플리케이션은 개발환경과 운영환경으로 나눠서 운영한다. 가령 환경에 따라 API 서버 주소가 다를 수 있다. 같은 소스 코드를 두 환경에 배포하기 위해서는 이러한 환경 의존적인 정보를 소스가 아닌 곳에서 관리하는 것이 좋다. 배포할 때마다 코드를 수정하는 것은 곤란하기 때문이다.
- 웹팩은 이러한 환경 정보를 제공하기 위해 DefinePlugin을 제공한다.

```js
const webpack = require("webpack")

export default {
  plugins: [new webpack.DefinePlugin({})],
}
```

- 빈 객체를 전달해도 기본적으로 넣어주는 값이 있다. 노드 환경정보인 process.env.NODE_ENV 인데 웹팩 설정의 mode에 설정한 값이 여기에 들어간다. "development"를 설정했기 때문에 어플리케이션 코드에서 process.env.NODE_ENV 변수로 접근하면 "development" 값을 얻을 수 있다.

index.js

```
console.log(process.env.NODE_ENV) // "development"
```

##### 2.3 HtmlWebpackPlugin

- 여기서부터 써드 파티 패키지에 대해 알아보자. HtmlWebpackPlugin은 HTML 파일을 후처리하는데 사용한다. 빌드 타임의 값을 넣거나 코드를 압축할수 있다.
- 이 플러그인으로 빌드하면 HTML파일로 아웃풋에 생성된다.

```js
new HtmlWebpackPlugin({
            title: "webpack-practice",
            template: "./index.html", // 템플릿 경로를 지정
            inject: "body",
            favicon: "./favicon.PNG"
            }),
```

- HtmlWebpackPlugin은 template 파일 안에서 lodash 문법을 사용할 수 있도록 한다.

index.html

```html
<!DOCTYPE html>
<html>
    <head>
        <title>
            <%= htmlWebpackPlugin.options.title %>
        </title>
    </head>
    <body>

    </body>
</html>
```

- 번들링 결과인 dist 디렉토리 내 index.html의 결과는 다음과 같다.

```html
<title>webpack-practice</title>
```

##### 2.4 MiniCssExtractPlugin

- 스타일시트가 점점 많아지면 하나의 자바스크립트 결과물로 만드는 것이 부담일 수 있다. 번들 결과에서 스트일시트 코드만 뽑아서 별도의 CSS 파일로 만들어 역할에 따라 파일을 분리하는 것이 좋다. 브라우져에서 큰 파일 하나를 내려받는 것 보다, 여러 개의 작은 파일을 동시에 다운로드하는 것이 더 빠르다.
- 개발 환경에서는 CSS를 하나의 모듈로 처리해도 상관없지만 프로덕션 환경에서는 분리하는 것이 효과적이다. **MiniCssExtractPlugin은 CSS를 별로 파일로 뽑아내는 플러그인이다.**

```js
    plugins: [
        ...(process.env.NODE_ENV === "production"
        ? [new MiniCssExtractPlugin({ filename: `[name].css` })]
        : []),
    ],
```

- 프로덕션 환경일 경우만 이 플러그인을 추가했다. filename에 설정한 값으로 아웃풋 경로에 CSS 파일이 생성된다.
- 개발 환경에서는 css-loader에의해 자바스크립트 모듈로 변경된 스타일시트를 적용하기위해 style-loader를 사용했다. 반면 프로덕션 환경에서는 별도의 CSS 파일으로 추출하는 플러그인을 적용했으므로 다른 로더가 필요하다.

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          process.env.NODE_ENV === "production"
            ? MiniCssExtractPlugin.loader // 프로덕션 환경
            : "style-loader", // 개발 환경
          "css-loader",
        ],
      },
    ],
  },
}
```

<h3>끝!</h3>
