---
title: Todo List
categories:
- Toy Project
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
--- 

input에 할 일을 입력해 할 일을 추가하고, 완료한 것을 표시하고 수정도 가능 하도록 만들어 보자. 완료한 것이거나 해야할 것을 삭제하는 컨트롤도 구현해보자. filter 기능을 사용해서 모두 보거나 해야 할일 혹은 완료된 것을 볼 수 있도록 해보자. 그리고 Router를 구현해서 filter 마다 URL을 다르게 표현해보자. 그리고 todoList의 데이터들을 localStorage에 저장해보자. 

#### 개발 환경 설정(Rollup)

```
npm i -D rollup
```

- rollup을 설치한다. 그 외에 필요 패키지는 아래와 같다. 

  - rollup-plugin-generate-html-template: HTML 템플릿 생성 플러그인
  - rollup-plugin-livereload: rollup을 dev server로 돌릴 때 watch 모드로 돌리는데, 변경이 감지되면 dist에 빌드가 되는데 그때 dev server를 reload 시켜주는 플러그인
  - rollup-plugin-scss : sass 파일을 읽기위한 플러그인
  - rollup-plugin-serve: dev server를 돌리기 위한 플러그인
  - rollup-plugin-terser: 빌드 할때 minify와 uglify하기 위한 플러그인
  - sass: rollup-plugin-scss를 사용하기 위한 패키지

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
