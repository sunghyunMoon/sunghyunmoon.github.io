---
title: 웹팩 실습 심화
categories:
- Webpack
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

이전 포스트에서는 웹팩의 개념과 간단한 실습을 해봤다. 이번 포스트에서는 웹팩을 이용해 개발 서버를 제공하고, **빌드 결과를 최적화할 수 있는 몇가지 방법**을 알아보자.

#### 웹팩 개발 서버

- 인터넷에 웹사이를 게시된다는 얘기는 인터넷이 연결된 컴퓨터에서 서버가 돌고, 서버 프로그램이 js, html과 같은 정적 파일들을 client에게 제공하는 형식이다. 

- 개발환경에서도 유사한 환경을 갖추는 것이 좋다. 배포환경과 맞춤으로써 배포시 잠재적인 문제를 확인할 수 있다. 프로트엔드 개발환경에서 이러한 개발용 서버를 제공해 주는 것이 webpack-dev-server다.

##### 설치 및 사용

- webpack-dev-server 패키지 설치

```
npm i -D webpack-dev-server
```

- 스크립트 등록

```
{
  "script": {
    "start": "webpack-dev-server"
  }
}
```

- npm start를 실행하면 아래와 같은 메세지를 확인할 수 있다.

```
npm start

> webpack-dev-server

ℹ ｢wds｣: Project is running at http://localhost:8080/
ℹ ｢wds｣: webpack output is served from /
ℹ ｢wds｣: Content not from webpack is served from
```

- 로컬 호스트의 8080 포트에 서버가 구동되어서 접속을 대기하고 있다는 것을 의미한다.
- 웹팩 서버는 파일 변화를 감지해 웹팩 빌드를 다시 수행하고 브라우져를 리프레쉬하여 변경된 결과물을 보여준다.

##### 기본 설정

- 웹팩 설정 파일의 devServer 객체에 개발 서버 옵션을 설정할 수 있다.

```
// webpack.config.js:
module.exports = {
  devServer: {
    contentBase: path.join(__dirname, "dist"),
    publicPath: "/",
    overlay: true,
    port: 8081,
    stats: "errors-only",
    historyApiFallback: true,
  },
}
```

- contentBase: 정적파일을 제공할 경로. 기본값을 웹팩 아웃풋.
- publicPath: 브라우져를 통해 접근하는 경로. 기본값은 '/'.
- overlay: 빌드시 에러나 경고를 브라우져 화면에 표시한다.
- port: 개발 서버 포트 번호를 설정한다. 기본값은 8080.
- stats: 메세지 수준을 정할 수 있다. 메세지는 웹팩 서버를 돌렸을 때 커맨드 창에 뜨는 메세지를 뜻한다. 'none', 'erros-only', 'minimal', 'normal', 'verbose'로 조절할 수 있다.
- historyApiFallback: 히스토리 api를 사용하는 SPA 개발시 설정한다. 404가 발생하면 index.html으로 리다이렉트한다. 

#### 최적화

- 코드가 많아지면 번들링된 결과물이 기가 바이트 단위로도 커질 수 있다. 그래서 어떻게 웹팩으로 번들 결과를 압축하고 상황에 따라서는 작은 파일 여러 개로 분리할 수 있는 최적화 방법에 대해 알아보자.

##### production 모드

- 가장 기본적인 방법은 mode 값 설정이다. 개발 환경의 "development" mode에서는 디버깅 편의를 위해 NamedChunksPlugin, NamesModulesPlugin을 사용한다. 
- DefinePlugin을 사용한다면 prcoess.env.NODE_ENV 값이 "development"로 설정되어 어플리케이션에 전역변수에 주입된다. 
- 반면 **mode를 "production"으로 설정하면 자바스크립트 결과물을 최소화 하기 위해 다음 일곱 개 플러그인을 사용**한다.

  - FlagDependencyUsagePlugin
  - FlagIncludedChunksPlugin
  - ModuleConcatenationPlugin
  - NoEmitOnErrorsPlugin
  - OccurrenceOrderPlugin
  - SideEffectsFlagPlugin
  - TerserPlugin

- DefinePlugin을 사용한다면 process.env.NODE_ENV 값이 "production"으로 설정되어 어플리케이션 전역변수로 들어간다.
- 그래서 NODE_ENV 값에 따라 모드를 설정하도록 웹팩 설정 코드를 아래와 같이 설정할 수 있다.

```
// webpack.config.js:
const mode = process.env.NODE_ENV || "development" // 기본값을 development로 설정

module.exports = {
  mode,
}
```

- 빌드 시에 production 모드로 설정하여 실행하도록 npm 스크립트를 추가한다.

package.json

```
{
  "scripts": {
    "start": "webpack-dev-server --progress",
    "build": "NODE_ENV=production webpack --progress"
  }
}
```

- production mode에서 빌드 결과물을 보면 난독화 플러그인에 의해서 코드가 난독화되어 있는것을 확인할 수 있다. 

##### optimization 속성으로 최적화

- HtmlWebpackPlugin이 html 파일을 압축한것 처럼 **css 파일도 빈칸을 없애는 압축을 하려면 optimize-css-assets-webpack-plugin을 사용**한다.

```
npm i -D optimize-css-assets-webpack-plugin
```

- 웹팩 설정에 추가

```
// webpack.config.js:
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin")

module.exports = {
  optimization: {
    minimizer: mode === "production" ? [new OptimizeCSSAssetsPlugin()] : [],
  },
}
```

- optimization.minimizer는 웹팩이 결과물을 압축할때 사용할 플러그인을 넣는 배열이다. 설치한 OptimizeCssAssetsPlugin을 전달해서 빌드 결과물중 css 파일을 압축하도록 했다. 

- production mode에 사용되는 TerserPlugin은 js 코드를 난독화하고 debugger 구문을 제거한다. 기본 설정 외에도 콘솔 로그를 제거하는 옵션도 있다. 배포 버전에는 로그를 감추는 것이 좋다.

```
npm i -D terser-webpack-plugin
```

- optimization.minimizer에 배열 추가

```
// webpack.config.js:
const TerserPlugin = require("terser-webpack-plugin")

module.exports = {
  optimization: {
    minimizer:
      mode === "production"
        ? [
            new TerserPlugin({
              terserOptions: {
                compress: {
                  drop_console: true, // 콘솔 로그를 제거한다
                },
              },
            }),
          ]
        : [],
  },
}
```

##### 코드 스플리팅

- 코드를 압축하는 방법 외에 번들 결과물을 여러개로 쪼개면 좀 더 브라우저 다운로드 속도를 높일 수 있다. 가장 단순한 것을 엔트리를 여러개로 분리하는 것이다. 

```
// webpack.config.js
module.exports = {
  entry: {
    main: "./src/app.js",
    controller: "./src/controller.js",
  },
}
```
 
- 빌드하면 엔트리가 두 개 생성되고 엔트리가 하나일때 보다 용량이 준다.
- SplitChunksPlugin은 코드를 분리할때 중복을 예방하는 플러그인이다. optimization.splitChunks 속성을 설정하는 방식이다.

```
// webpack.config.js:
module.exports = {
  optimization: {
    splitChunks: {
      chunks: "all",
    },
  },
}
```

- 이렇게 하면 중복되는 코드를 제거하고 엔트리를 다시 빌드한다. 이 방식은 엔트리 포인트를 적절히 분리해야기 때문에 손이 많이 간다. 반면 자동으로 변경해주는 방식이 있는 이를 **dynamic import**라 부른다.
- 기존 코드는 아래와 같다.

```
import controller from "./controller"

document.addEventListener("DOMContentLoaded", () => {
  controller.init(document.querySelector("#app"))
})
```

- 이를 dynamic import로 변경하면 아래와 같다.

```
function getController() {
  return import(/* webpackChunkName: "controller" */ "./controller").then(m => {
    return m.default
  })
}

document.addEventListener("DOMContentLoaded", () => {
  getController().then(controller => {
    controller.init(document.querySelector("#app"))
  })
})
```

- import 함수로 가져올때 주석으로 webpackHunkName: "controller"를 전달했는데, 이는 웹팩이 이 파일을 처리할때 청크로 분리하는데 그 청크 이름을 설정한 것이다.

##### externals

- 마지막 방법으로는 **애초에 번들하지 말아야할 대상들은 빌드 범위에서 빼는 것**이다. 
- 예를 들어, axios같은 라이브러리는 우리가 직접 만드는 코드가 아니라 npm에서 다운받은 코드다. 이미 빌드되어 있는 결과가 있다. 이것은 웹팩에서 빌드하지 않고 다운받은 파일에서 바로 사용해도 된다. 
- 웹팩 설정 파일 중 externals 설정이다.

```
// webpack.config.js:
module.exports = {
  externals: {
    axios: "axios",
  },
}
```

- 웹팩으로 빌드할때 axios 모듈을 사용하는 부분이 있으면 전역 변수 axios를 사용하는 것으로 간주하자는 뜻이다. 대신 저역변수로 사용하려면 axios를 가져와야 된다.
- axios는 node_modules 안에 위치한다. 정확히 node_modules/axios/dist/axios.min.js다. 이 파일을 html에서 로딩하면 된다. 
- **axios.min.js 파일을 dist 폴더에 옮기고 파일을 복사하는 CopyWebpackPlugin을 설치**한다. 

```
npm i -D copy-webpack-plugin
```

- 플러그인을 사용해 라이브러리르 복사한다.

```
const CopyPlugin = require("copy-webpack-plugin")

module.exports = {
  plugins: [
    new CopyPlugin([
      {
        from: "./node_modules/axios/dist/axios.min.js",
        to: "./axios.min.js", // 목적지 파일에 들어간다
      },
    ]),
  ],
}
```

- index.htmlk에서는 axios를 로딩하는 코드를 추가한다.

```
<!-- src/index.html -->
  <script type="text/javascript" src="axios.min.js"></script>
</body>
</html>
```

- 이렇게 써드파티 라이브러리를 externals로 분리하면 용량이 감소할 뿐만 아니라 빌드 시간도 줄어들고 덩달아 개발 환경도 가벼워 질 수 있다.

### 참고 문헌

https://jeonghwan-kim.github.io/series/2020/01/02/frontend-dev-env-webpack-intermediate.html#4-%EC%B5%9C%EC%A0%81%ED%99%94


<h3>끝!</h3>
