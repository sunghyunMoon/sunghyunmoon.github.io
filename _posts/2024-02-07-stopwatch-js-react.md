---
title: VanillaJS로 스톱워치 만들기
categories:
- ToyProject
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

이번에는 Vanilla JS를 이용해 스톱워치를 만들어보자. 

### 스톱워치 모듈

| 상태 이름 | 설명 |
| --- | --- |
| _interval | start() 시 실행되는 [setInterval](https://developer.mozilla.org/en-US/docs/Web/API/setInterval)이 리턴하는 intervalID |
| _centisecond | 현재 스톱워치의 시간 (단위: centisecond) |
| _lapCount | 현재 스톱워치의 누적 Lap 갯수 |
| _prevLap | 직전 Lap 시간 |


- 모듈 메서드 (함수)

| 메소드 이름 | 기능 | 리턴값 |
| --- | --- | --- |
| centisecond | 현재 centisecond 값 가져오기 | _centisecond |
| start | 스톱워치 시작하기 | null |
| pause | 스톱워치 중단(일시정지)하기 | null |
| createLap | 스톱워치 Lap 생성하기 | [_lapCount, 현재 Lap 시간] |
| reset | 스톱워치 리셋하기 | null |

### 시작, 중단 기능 만들기

- 시작, 중단 버튼 클릭 이벤트 핸들러는 아래와 같다. 

```js
const setTime = (time) => {
    timer.innerText = time;
};

const handleStartStop = () => {
    if (isRunning === false) {
        isRunning = true;
        stopWatch.start();
        setInterval(() => {
            setTime(convertCentiSecondtoMinSecond(stopWatch.centisecond));
        }, 10);
        startStopBtnLabel.innerText = '중단';
        lapResetBtnLabel.innerText = '랩';
    } else {
        isRunning = false;
        stopWatch.pause();
        setTime(stopWatch.centisecond);
        startStopBtnLabel.innerText = '시작';
        lapResetBtnLabel.innerText = '리셋';
    }
    startStopBtn.classList.toggle('bg-green-600');
    startStopBtn.classList.toggle('bg-red-600');
```

- 스톱워치가 멈춰있는 경우, stopWatch 객체를 start시키고, 10ms 마다 타이머의 innerText를 업데이트한다.
- 스톱워치가 구동중인 경우, stopWatch 객체를 pause시킨다.
- 버튼 클릭시 마다 버튼의 bg 색이 바껴야 하기 때문에 에를 classList의 toggle을 이용해서 구현했다. 

### 랩/리셋 기능 구현하기

- 랩 L 버튼을 클릭하면 Lap Count가 함께 명시된 랩이 하나씩 기록되면서 최신 Lap이 맨 위에 추가 된다.

```js
const handleLapReset = () => {
    if (isRunning === true) {
        appendLap();
    } else {
        stopWatch.reset();
        setTime(convertCentiSecondtoMinSecond(0));
        clearLaps();
    }
};

const appendLap = () => {
    const [lapCnt, lapTime] = stopWatch.createLap();
    const lap = document.createElement('li');
    lap.setAttribute('data-time', lapTime);
    lap.className = 'flex justify-between py-2 px-3 border-b-2';
    lap.innerHTML = `  
                        <span>랩 ${lapCnt}</span>
                        <span>${convertCentiSecondtoMinSecond(lapTime)}</span>
                    `;
    laps.prepend(lap);

}
```

- 최신 lap이 가장 위쪽에 append되어야 하기 때문에 prepend 메서드를 이용했다. 

### 키보드 조작 기능 구현

- 아래는 키 조작 이벤트 핸들러인 handleKeyDown이다. 

```js
document.documentElement.addEventListener('keydown', (e) => handleKeyDown(e));

const handleKeyDown = (e) => {
    console.log(e.code);
    switch (e.code) {
        case 'KeyL':
            handleLapReset();
            break;
        case 'KeyS':
            handleStartStop();
            break;
        default:
            return;
    }
};
```

### 최단, 최장 기록 강조 기능 구현

```js
const appendLap = () => {
    ...

    // first
    if (maxLap === undefined) {
        maxLap = lap;
        return;
    }
    // second
    if (minLap === undefined) {
        const maxTime = parseInt(maxLap.getAttribute('data-time'));
        if (lapTime > maxTime) {
            minLap = maxLap;
            maxLap = lap;
        } else {
            minLap = lap;
        }
        maxLap.classList.add('text-red-600');
        minLap.classList.add('text-green-600');
        return;
    }
    const maxTime = parseInt(maxLap.getAttribute('data-time'));
    const minTime = parseInt(minLap.getAttribute('data-time'));
    if (lapTime < minTime) {
        minLap.classList.remove('text-green-600');
        minLap = lap;
    } else if (lapTime > maxTime) {
        maxLap.classList.remove('text-red-600');
        maxLap = lap;
    }
    maxLap.classList.add('text-red-600');
    minLap.classList.add('text-green-600');
};
```

- 최단, 최장 Lap 기록은 Lap이 새롭게 추가될때 정해지기 때문에 위에서 구현한 appendLap 함수 내에서 구현했다. 
- 최단, 최장 Lap에 해당하는 HTMLElement를 찾아서 classList로 스타일링을 해야하기 때문에, 전역변수로 minLap과 maxLap을 관리했다. 
- HTMLElement에는 lapTime에 대한 기록이 없기 때문에 해당 lapTime을 dataSet 객체에 저장했다.
- 따라서 lap이 하나 추가될때 마다 min/maxLap HTMLElement의 dataSet 객체에 있능 lapTime를 받아서 비교한 후, min/maxLap을 업데이트했다. 


### 완성본

<a href="https://sunghyunmoon.github.io/stopwatch-vanillajs/src/">
완성본 보러가기
</a>

<h3>끝!</h3>
