---
title: React로 스톱워치 만들기
categories:
- ToyProject
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

이번에는 React JS를 이용해 스톱워치를 만들어보자. 

### 컴포넌트 구성

- 컴포넌트 구성은 우선 스톱워치를 총괄하는 컴포넌트인 StoWatch 컴포넌트를 생성했다.

```js
const { centisecond, start, pause, createLap, reset, isRunning, laps } =
        useTimer();

<section className="w-fit bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4 flex flex-col justify-center m-auto mt-36 max-w-sm">
            <Timer centisecond={centisecond} />
            <div className="flex justify-between text-white pb-8 text-sm select-none">
                <Button
                    color="bg-gray-600"
                    label={isRunning === true ? '랩' : '리셋'}
                    code="L"
                    handleBtnClick={handleLapRest}
                    ref={lapResetRef}
                />
                <Button
                    color={isRunning === true ? 'bg-red-600' : 'bg-green-600'}
                    label={isRunning === true ? '중단' : '시작'}
                    code="S"
                    handleBtnClick={handleStartStop}
                    ref={startStopRef}
                />
            </div>
            <Laps laps={laps} />
        </section>
```
- 가장 위에는 실시간 타이머를 보여주는 Timer 컴포넌트, 그 밑에 Button 컴포넌트, 그 밑에 Laps 컴포넌트를 뒀다.
- StopWatch 컴포넌트에서 useTimer 훅을 호출하여 상태와 로직을 관리한 뒤, 해당 로직을 하위 Button 컴포넌트에 이벤트 핸들러를 내려주는 방식으로 구현했다.
- 다시 말해, 타이머 모듈의 상태 관리를 단일화하여 여러 상태들을 한 곳(StopWatch)에서만 관리하고, 하위 컴포넌트들은 그 상태를 props로 전달 받아 표시하거나, 상위에서 내려준 핸들러를 통해 상태를 변경하게 된다.
- 이를 통해, **Button 컴포넌트는 시간 측정과 관련된 로직을 전혀 알 필요 없이, 단순히 버튼 UI의 역할**에만 충실할 수 있도록 했다. 

### 시작/중단, 랩/리셋 기능 만들기

```js
const handleStartStop = () => {
        if (isRunning === false) {
            start();
        } else {
            pause();
        }
    };
    const handleLapRest = () => {
        if (isRunning === false) {
            reset();
        } else {
            createLap();
        }
    };

<Button
    color={isRunning === true ? 'bg-red-600' : 'bg-green-600'}
    label={isRunning === true ? '중단' : '시작'}
    code="S"
    handleBtnClick={handleStartStop}
    ref={startStopRef}
/>
```

- useTimer의 isRunning 변수를 통해 Button 컴포넌트의 color, label props를 제어했다.
- StopWatch 컴포넌트에서 Button 컴포넌트에 이벤트 핸들러를 props로 전달해줬다.

### 랩 기능 구현

- 랩 버튼을 누르면 useTimer의 createLap 메서드가 수행이 되고, 결과 값으로 laps를 반환한다.
- 결과값인 laps는 StopWatch 컴포넌트의 state이기 때문에 StopWatch 컴포넌트 리렌더 되면서 Laps 컴포넌트에 laps를 props로 전달한다.

### 키보드 조작 기능 구현

- keydown 이벤트는 document에 이벤트 리스너를 추가해야하기 때문에 useEffect 훅을 사용했다.

```js
const handleKeyDown = (e) => {
        if (e.code === 'KeyL') {
            lapResetRef.current.click();
        } else if (e.code === 'KeyS') {
            startStopRef.current.click();
        }
};
useEffect(() => {
    document.addEventListener('keydown', (e) => handleKeyDown(e));
}, []);
```

- 그리고 key 이벤트는 버튼을 클릭했을때와 동일하게 처리하기 위해 버튼의 ref에대해 click 이벤트를 수행하도록 했다.

### 최단, 최장 기록 강조 기능 구현

-  최단, 최장 기록 강조 기능을 구현하기 위한 Laps 컴포넌트 로직은 아래와 같다. 

```js
const Laps = ({ laps }) => {
    const sortedLaps = [...laps].sort((lap1, lap2) => lap1[1] - lap2[1]);
    const maxLapNum = sortedLaps.length && sortedLaps[sortedLaps.length - 1][0];
    const minLapNum = sortedLaps.length && sortedLaps[0][0];

    const createMinMaxStyle = (lap) => {
        if (laps.length < 2) return;
        if (lap[0] === minLapNum) return 'text-green-600';
        else if (lap[0] === maxLapNum) return 'text-red-600';
    };

    return (
        <article className="text-gray-600 h-64 overflow-auto border-t-2">
            <ul id="laps">
                {/* 추가되는 lap은 아래의 HTML 코드 형식을 사용해 추가해주세요.  */}
                {laps.map((lap) => (
                    <li
                        className={`${createMinMaxStyle(
                            lap
                        )} flex justify-between py-2 px-3 border-b-2`}
                        key={lap[0]}
                    >
                        <span>랩 {lap[0]}</span>
                        <span>{formatTime(lap[1])}</span>
                    </li>
                ))}
            </ul>
        </article>
    );
};
```

- 우선 laps 배열을 복사해 sortedLaps로 정렬했다.
- 그리고 최단/ 최장 Lap 번호를 추출하고, map을 통해 렌더링시에 createMinMaxStyle 메서들를 생성해 Lap 번호가 같은 경우 스타일을 추가했다.
- 그리고 Lap이 한 개인 경우는 최단/ 최장 기능이 동작하지 않기때문에 createMinMaxStyle 메서드에서 early return 처리했다.


### 완성본

<a href="https://sunghyunmoon.github.io/stopwatch-vanillajs/src/">
완성본 보러가기
</a>

<h3>끝!</h3>
