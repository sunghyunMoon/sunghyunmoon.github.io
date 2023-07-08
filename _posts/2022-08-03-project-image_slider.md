---
title: 이미지 슬라이더 만들기
categories:
- Toy Project
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

두 번째 프로젝트로 이미지 슬라이드를 만들어 보자. html과 css를 이용해 이미지 슬라이드를 만들고, next, previous 버튼을 이용해 이미지 이동 버튼을 구현해보자. 그리고 indicator를 개발해서 원하는 index로 이미지가 이동하도록 해보자. 기본 상태가 autoplay 상태로 하고, stop을 할 수 있도록 구현해보자.

#### autoplay Icon 버튼

- Font Awesome이라는 오픈 소스 라이브러리를 사용해봤다. Font Awesome의 Free 버전에 있는 play 아이콘을 사용해보자. 

```
npm install --save @fortawesome/fontawesome-free
```

- Font Awesome을 css 상단에 불러와서 사용해보자. node_modules에서 fontawesome의 all.min.css 설치된 경로를 가져온다.

```
@import url('~@fortawesome\fontawesome-free\css\all.min.css');
```

- 이렇게 하면 fontawesome을 사용하기 위한 준비가 완료되었다. 
- html에서 이미지 파일을 불러와야하기 때문에 이와 관련해 웹팩 설정을 해보자. 
- webpack.config.js에서 이미지 로더 관련 설정을 하자.

```js
module: {
        rules: [
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, "css-loader"]
            },
            {
                test: /\.jpeg$/,
                type: 'asset/inline'
            },
        ]
    },
```

- 이미지를 읽을때는 웹팩에 있는 내장된 로더를 읽기 위함이다. 

#### HTML Architecture

<br>

<div><img src= "/assets/img/post/image_slider_html.PNG"></div>

- 전체를 slider-wrap을 wrapping했고, slider-wrap 자식으로 ul 태그가 있고, 그 자식으로 list태그(슬라이드) 들이 있다. 그리고 next/previous 버튼이 있고, indicator와 autoplay 버튼이 차례대로 있다. 

```css
.slider-wrap {
  width: 1000px;
  height: 400px;
  margin: 50px auto;
  position: relative;
  overflow: hidden;
}
```

- slider-wrap은 relative(기존) 기준으로 위/아래는 margin은 50px로 주고, 수평축은 가운데 정렬했다. 

```css
.slider-wrap ul.slider {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0px;
}
```

- ul.slider에 position을 absolute로 줘 slider-wrap을 기준으로 레이아웃을 잡았다. 

```css
.slider-wrap ul.slider li {
  float: left;
  width: 1000px;
  height: 400px;
}
```

- li 들은 줄바꿈 처리가 되지 않고 float: left 처리를 해서 오른쪽으로 줄지어서 있도록 했다. 
- 1000px 씩 이미지가 width를 잡고 있는데, next 버튼을 누를때마다 ul.slider의 left를 1000px씩 shifting 시켜 다음 image로 넘겨볼 생각이다. 

```css
.btn {
  position: absolute;
  width: 50px;
  height: 60px;
  top: 50%;
  margin-top: -25px;
  line-height: 57px;
  text-align: center;
  cursor: pointer;
  background: rgba(0, 0, 0, 0.1);
  z-index: 100;
  user-select: none;
  transition: 0.1s;
}
.next {
  right: -50px;
  border-radius: 7px 0px 0px 7px;
  color: white;
}
.previous {
  left: -50px;
  border-radius: 0px 7px 7px 7px;
  color: white;
}
.slider-wrap:hover .next {
  right: 0px;
}
.slider-wrap:hover .previous {
  left: 0px;
}
```

- 버튼 또한 slider-wrap을 기준으로 레이아웃을 했다. 
- slider-wrap을 hover하면 각각 -50px에서 slider 안으로 들어오도록 했다. 

#### next/prev 버튼 구현

- next/previous 버튼을 눌렀을 때 슬라이더를 왼쪽/오른쪽으로 가도록 구현해보자. 
- ImageSlider 클래스를 만들고, 번들링 되도록 index.js에 ImageSlider 인스턴스를 생성했다. 

index.js

```js
import '../css/style.css';
import '@fortawesome/fontawesome-free/js/all.js';
import { ImageSlider } from './ImageSlider.js';

new ImageSlider();
```

- 프로퍼티로 currentPosition, sliderNumber, sliderWidth를 추가했다. 
- sliderWidth 만큼 왼쪽/오른쪽으로 옮겨줄 것이고, currentPosition은 현재 슬라이더의 index 정보이고, sliderNumber는 총 이미지 갯수다. 

```js
    assignElement() {
        this.sliderWrapEl = document.getElementById('slider-wrap');
        this.sliderListEl = this.sliderWrapEl.querySelector('#slider');
        this.previousBtnEl = this.sliderWrapEl.querySelector('#previous');
        this.indicatorWrapEl = this.sliderWrapEl.querySelector('#indicator-wrap');
        this.controlWrapEl = this.sliderWrapEl.querySelector('#control-wrap');
    }

    initSliderNumber() {
        this.#sliderNumber = this.sliderListEl.querySelectorAll('li').length;
    }   

    initSliderWidth() {
        this.#sliderWidth = this.sliderWrapEl.clientWidth;
    }
    initSliderWidth() {
        this.#sliderWidth = this.sliderWrapEl.clientWidth;
    }
```

- addEvent 메서드를 통해 eventListener를 등록하자.

```js
    addEvent() {
        this.nextBtnEl.addEventListener('click', this.moveToRight.bind(this));
        this.previousBtnEl.addEventListener('click', this.moveToLeft.bind(this));
    }
```

- moveToLeft/Right 메서드를 통해 이동 시키는 동작을 구현해보자.

```js
    moveToRight() {
        this.#currentPosition += 1;
        if (this.#currentPosition === this.#sliderNumber) {
        this.#currentPosition = 0;
        }
        this.sliderListEl.style.left = `-${
        this.#sliderWidth * this.#currentPosition
        }px`;
    }
```

- currentPosition 값에다 sliderWidth를 곱해서 left로 -값을 주면 오른쪽으로 이동하는 것처럼 보인다. 그리고 마지막 슬라이드에서 오른쪽으로 이동시 처음으로 돌아가기 위해서 currentPosition이 sliderNumber가 되면 다시 0으로 초기화 했다. 
- 반대의 경우(moveToLeft)도 마찬가지다.

#### indicator 구현

- indicator 버튼을 누르면 한 스텝씩 이동하지 않고 바로 해당되는 슬라이더로 이동할 수 있게 구현해보자. 

```html
<div class="indicator-wrap" id="indicator-wrap">
    <ul>
    </ul>
</div>
```

- HTML상으로 indicator-wrap안에 ul 태그의 li로 들어가야되는데, 슬라이드 갯수를 통해서 스크립트를 이용해 동적으로 생성되도록 구현 해보자. 

```js
createIndicator() {
    const docFragment = document.createDocumentFragment();
    for (let i = 0; i < this.#sliderNumber; i += 1) {
      const li = document.createElement('li');
      li.dataset.index = i;
      docFragment.appendChild(li);
    }
    this.indicatorWrapEl.querySelector('ul').appendChild(docFragment);
}
```

- 위와 같이 sliderNumber 만큼 indicator li 태그를 만들어 documentFragment에 붙여주고, for문이 끝난후 docuemntFragment를 indicator-wrap에 append했다. 

```js
    setIndicator() {
        // index 활성화 없음
        this.indicatorWrapEl.querySelector('li.active')?.classList.remove('active');
        // index에 따라서 활성화
        this.indicatorWrapEl
        .querySelector(`ul li:nth-child(${this.#currentPosition + 1})`)
        .classList.add('active');
    }

    onClickIndicator(event) {
        const indexPostion = parseInt(event.target.dataset.index, 10);
        if (Number.isInteger(indexPostion)) {
            this.#currentPosition = indexPostion;
            this.sliderListEl.style.left = `-${
            this.#sliderWidth * this.#currentPosition
            }px`;
            this.setIndicator();
        }
    }
```

- setIndicator를 통해 하나의 indicator를 active를 시키는데, 우선 li 중 기존 active 클래스들을 모두 제거해 초기화 했다. 
- 그리고 currentPosition에 따라 active 시켰다. n-th child Selector를 이용했고, index가 1부터 시작하기 때문에 currentPosition + 1을 해줬다. 
- 또한, moveToLeft/Right 메서드에 setIndicator 메서드를 추가해 next/prev 버튼을 누를때도 currentPosition에 따라 indiciator가 활성화 되도록 했다. 
- onClickIndicator 메서드를 통해 indicator를 클릭했을 때 해당 슬라이드로 이동하도록 했다. 클릭한 li 태그의 data-index를 받아와 currentPosition으로 설정하고, setIndicator를 호출해 indicator도 sync를 맞춰주었다. 

#### autoplay 구현

- default로 autoplay를 구동시키고, pause 버튼을 눌렀을 때 autoplay를 멈추도록 구현해보자. 

```html
<div class="control-wrap play" id="control-wrap">
    <i class="fa fa-pause" id="pause" data-status="pause"></i>
    <i class="fa fa-play" id="play" data-status="play"></i>
</div>
```

```css
.play .fa-play {
  display: none;
}

.play .fa-pause {
  display: block;
}

.pause .fa-play {
  display: block;
}

.pause .fa-pause {
  display: none;
}
```

- control-wrap 클래스에서 play 클래스면 puase 버튼이 뜨고, pause 클래스면 play 버튼이 뜨도록 html과 css를 구성했다.

```js
this.controlWrapEl = this.sliderWrapEl.querySelector('#control-wrap');
```

- 해당 control-wrap 클래스에대해 프로퍼티를 추가했다.

```js
initAutoPlay() {
    this.#intervalId = setInterval(this.moveToRight.bind(this), 3000);
}
```

- initAutoPlay를 통해 3초 간격마다 moveToRight을 호출한다. 그리고 타이머 기능을 멈추기 위해서 setInterval이 반환하는 intervalId를 프로퍼티로 저장했다. 

```js
    togglePlay(event) {
        console.log(event);
        if (event.target.parentElement.dataset.status === 'play') {
            this.#autoPlay = true;
            this.controlWrapEl.classList.add('play');
            this.controlWrapEl.classList.remove('pause');
            this.initAutoPlay();
        } else if (event.target.parentElement.dataset.status === 'pause') {
            this.#autoPlay = false;
            this.controlWrapEl.classList.add('pause');
            this.controlWrapEl.classList.remove('play');
            clearInterval(this.#intervalId);
        }
    }
```

- togglePlay 메서드를 click 이벤트 핸들러로 등록했다. pause 버튼을 누르면 clearInterval을 통해 타이머를 종료했다.
- 그리고 interval이 돌고 있고, 3초가 지나지 않은 상태에서 next/prev 버튼을 누르면 다음 슬라이드로 넘어가고 3초일때 또 넘어가게 된다. 
- 그래서 autoPlay 변수를 이용해 moveToLeft/Right 시 autoPlay가 true인 경우 타이머를 재시작하도록 했다.

```js
moveToRight() {
    this.#currentPosition += 1;
    if (this.#currentPosition === this.#sliderNumber) {
      this.#currentPosition = 0;
    }
    this.sliderListEl.style.left = `-${
      this.#sliderWidth * this.#currentPosition
    }px`;
    if (this.#autoPlay) {
      clearInterval(this.#intervalId);
      this.#intervalId = setInterval(this.moveToRight.bind(this), 3000);
    }
    this.setIndicator();
}

```

- 완성본은 아래 링크 클릭!!

<a href="https://sunghyunmoon.github.io/image_slider/">완성본 보러가기</a>

<h3>끝!</h3>
