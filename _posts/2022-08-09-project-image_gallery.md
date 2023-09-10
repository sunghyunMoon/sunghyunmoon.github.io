---
title: 이미지 갤러리
categories:
- Toy Project
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

 

#### React 프로젝트 생성

- 번들러, 바벨등 일일히 install하기 보다는 create react-app을 이용해 리액트 프로젝트를 생성했다. customizing이 조금 힘들수는 있지만, 간단한 프로젝트이기 때문에 무리 없을것 같다. 

```
yarn create react-app gallery --template typescript
```

- creat react-app 프로젝트의 기본 구조를 remind 겸 정리하고 가자.
    - node_modules : 설치된 외부 모듈을 import하면 node.js는 node_modules에서 해당 모듈을 찾는다.
    - public : index.html과 같은 정적 파일들이 위치한다.
        - index.html : 서버에서 읽고, 브라우저가 표출하는 파일이다. 다른 파일들은 삭제하거나 이름을 바꾸어도 상관 없지만 public/index.html과 src/index.js는 해당 이름으로만 사용하여야 한다.
        - manifext.json : 웹앱 메타데이터. 홈 화면에서 보여지는 앱 이름, 아이콘, 디스플레이 유형 등등을 설정할 수 있다. 
        - robot.txt : 웹 크롤러를 위한 정보.
        - favicon.ico : 브라우저의 주소창에 표시되는 웹 페이지 대표 아이콘.
    - src : src/ 내부에 위치한 파일들만 webpack에 의해 processed된다. src/ 외부에 위치한 js, css 파일들은 webpack이 찾을 수 없으므로 src/ 내부에 두도록 하자.
        - index.js/tsx : ReactDom이 App 컴포넌트를 document 내 id 값이 root인 태그 안에 렌더링한다.
        - App.js : root가 되는 리액트 컴포넌트.
    - package.json : 프로젝트 메타 데이터. 프로젝트의 종속성을 처리하고 패키지 매니저가 프로젝트를 식별할 수 있는 정보와 프로젝트 설명, 버전, 라이센스 정보 등 기타 메타데이터를 제공.

#### Main UI 구현

<div><img src= "/assets/img/post/image_gallery_main.PNG"></div>

- Main UI는 위와 같고, 아래는 App 컴포넌트 및 App.css다.

```js
function App() {
  return (
    <div className="App">
      <div className='container'>
        <div className='initial-box'>
          <div className='text-center'>
            이미지가 없습니다.<br/>
            이미지를 추가해주세요.          
          </div>
          <div className='plus-box'>
            +
          </div>
        </div>
      </div>
    </div>
  );
}
```

```css
.text-center {
  text-align: center;
}

.plus-box {
  width: 100px;
  height: 100px;
  border: solid 1px #707070;
  font-size: 24px;
  display: flex;
  justify-content: center;
  align-items: center;
  line-height: 24px;
}

.initial-box {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  gap: 80px;
}

.container {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
```

- 매우 간단하다. text와 plus-box를 wrapping하는 initial-box있고, initibal-box를 화면의 수평/수직 가운데 정렬을 하기 위해 container를 뒀다.

#### 이미지 갤러리 구현

- 이미지를 받으면 출력을 해주는 컴포넌트를 만든다.

```js
function ImageBox(props: {
    src: string
}) {
    return <div className="image-box">
        <img src={props.src} />
    </div>
}

export default ImageBox;
```

- 버튼을 클릭하면 파일을 올릴 수 있도록 구현하자. + 박스를 클릭하면 마치 input을 클릭한 것처럼 구현한다.
- useRef를 이용해 InputElement를 참조하고, plux-box의 click eventhandler에 해당 InputElement의 click이 되도록 구현했다. 그리고 InputElement는 UI로 보일 필요가 없기 때문에 display:none으로 설정했다.

```js
const inpRef = useRef<HTMLInputElement>(null);

    <input type='file' ref={inpRef} />
    <div className='plus-box' onClick={() => {
    inpRef.current?.click()
    }}>
    +
    </div>
```

- 자, 그럼 본격적으로 local에 있는 imageURL을 데이터 URL로 변경해 img 태그로 표현해보자.

Conversion from local image URL to Data URL

```js
    <input type='file' ref={inpRef} onChange={(event) => {
        const file = event.currentTarget.files?.[0];
        if (file) {
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onloadend = (event) => {
            setImageList(prev => [...prev, event.target?.result as string]);
            };
        }
    }} />
    {
        imageList.map((path, index) => <ImageBox key={path + index} src={path}/>)
    }
    <div className='plus-box' onClick={() => {
        inpRef.current?.click()
    }}>
    +
    </div>
```

- fileReader는 웹 브라우저에서 JS를 사용하여 파일을 비동기적으로 읽을수 있는 객체다. 이것을 이용해 Data URL을 만들 수 있다.
- onloadend 이벤트를 이용해 load가 완료되면 result를 imageList로 추가한다.


<a href="https://sunghyunmoon.github.io/image-gallery/">완성본 보러가기</a>

<h3>끝!</h3> 
