---
title: 3.5장 노드 내장 모듈 사용하기
categories:
- Node JS
feature_image: "https://w7.pngwing.com/pngs/445/982/png-transparent-node-js-express-js-javascript-mongodb-npm-logo-open-leaf-text-logo.png"
---

노드는 웹 브라우저에서 사용되는 자바스크립트보다 더 많은 기능을 제공한다. 운영체제 정보에도 접근할 수 있고, 클라이언트가 요청한 주소에 대한 정보도 가져올 수 있다. 노드에서 이러한 기능을 하는 모듈을 제공한다.
이번 챕터에서는 버전과 상관없이 안정적이고 유용한 기능을 지닌 모듈 위주로 알아보자!

##### 3.5.1 os

- 웹 브라우저에 사용되는 자바스크립트는 운영체제의 정보를 가져올 수 없지만, 노드는 OS 모듈에 정보가 담겨 있어 정보를 가져올 수 있다.
- 내장 모듈인 os를 불러오려면 require( 'os') 또는 require('node：os')를 하면 된다.
    - os.arch()： process.arc
    - os.platformO： process.platform
    - os.type()： 운영체제의 종류
    - os.uptime()： 운영체제 부팅 이후 흐른 시간(초)
    - os.hostname()： 컴퓨터의 이름
    - os.release()： 운영체제의 버전
    - os.homedir()： 홈 디렉터리 경로
    - os.tmpdir()： 임시 파일 저장 경로
    - os.cpus()： 컴퓨터의 코어 정보
    - os.freemem()： 사용 가능한 메모리
    - os.totalmem()： 전체 메모리 용량

- os 모듈은 주로 컴퓨터 내부 자원에 빈번하게 접근하는 경우 사용된다. 즉, 일반적인 웹 서비스를 제작할 때는 사용 빈도가 높지 않다. 하지만 운영체제별로 다른 서비스를 제공하고 싶을
때 os 모듈이 유용할 것이다.

##### 3.5.2 path

- 폴더와 파일의 경로를 쉽게 조작하도록 도와주는 모듈이다. path 모듈이 필요한 이유 중 하나는 운영체제별로 경로 구분자가 다르기 때문이다. 크게 윈도 타입과 POSIX 타입으로 구분된
다. POSIX는 유닉스 기반의 운영체제들로 맥과 리눅스가 속해 있습니다.
    - 윈도: C:\Users\ZeroCho처럼 \로 구분
    - POSIX：/home/zerocho처럼 /로 구분
- path 모듈의 속성과 메서드를 알아보자
    - path.sep： 경로의 구분자(윈도는 \, POSIX는 /)    
    - path.delimiter： 환경 변수의 구분자
    - path.dirname（경로）: 파일이 위치한 폴더 경로
    - path.extname（경로）: 파일의 확장자
    - path.basename（경로, 확장자）: 파일의 이름（확장자 포함）을 표시
    - path.parse（경로）: 파일 경로를 root, dir, base, ext, name으로 분리
    - path.format（객체）: path.parse（）한 객체를 파일 경로로 합침
    - path.normalize（경로）: /나 \를 실수로 여러 번 사용했거나 혼용했을 때 정상적인 경로로 변환
    - path.isAbsolute（경로）: 파일의 경로가 절대경로인지 상대경로인지
    - path.relative（기준경로, 비교경로）: 경로를 두 개 넣으면 첫 번째 경로에서 두 번째 경로로 가는 방법을 알림
    - path.join/resolve（경로, •••）： 여러 인수를 넣으면 하나의 경로로 합침
- join과 resolve 차이
    - path, join과 path.resolve 메서드는 비슷해 보이지만동작 방식이 다르다. /를 만나면 **path.resolve는 절대경로로 인식해서 앞의 경로를 무시**하고, **path, join은 상대경로로 처리**한다.

```js
path.join('/a', '/b', 'c'); // 결과 : /a/b/c
path.join('/a', '/b', 'c'); // 결과 : /b/c
```


##### 3.5.3 url

- 인터넷 주소를 쉽게 조작하도록 도와주는 모듈이다. url 처리에는 크게 두 가지 방식이 있다. 하나는 노드 버전 7에서 추가된 WHATWG(웹 표준을 정하는 단체의 이름) 방식의 url이고, 다른 하나는 예전부터 노드에서 사용하던 방식의 url이다. 요즘은 WHATWG 방식만 사용한다. 브라우저에서도 WHATWG 방식을 사용하므로 호환성이 좋다.

```js
const url = require('url')；
const { URL } = url；
const myURL = new URL('http://www.gilbut.co.kr/book/bookList.aspx7sercate1=001001000#anchor')；
console.log('new URL()：', myURL)；
console.log('url.format()：', url.format(myURL))；
```

- url 모듈 안에 URL 생성자가 있다. 참고로 URL은 노드 내장 객체이기도 해서 require할 필요는 없다. 이 생성자에 주소를 넣어 객체로 만들면 주소가 부분별로 정리된다. 이 방
식이 WHATWG의 url이다. 

```js
new URL()： URL {
href： 'http://www.gi.lbut.co.kr/book/bookl_ist.aspx?sercate1 =001001 OOONanchor',
origin: 'http://www.gilbut.co.kr',
protocol： 'http：',
username： '',
password： '',
host: 'www.gilbut.co.kr',
hostname: 'www.gilbut.co.kr',
port：'',
pathname： '/book/bookList.aspx',
search: '?sercate1=001001000',
searchParams： URLSearchParams { 'sercatel' => '001001000' },
hash： '#anchor'
}
url.format(): http://www.gilbut.co.kr/book/bookList.aspx?sercate1=001001000#anchor
```

콘솔

```
new URL()： URL {
href： ' http://www.gi.lbut.co.kr/book/bookl_ist.aspx?sercate1=001001 OOONanchor',
origin: 'http://www.gilbut.co.kr',
protocol： 'http：',
username： '',
password： '',
host: 'www.gilbut.co.kr',
hostname: 'www.gilbut.co.kr',
port：'',
pathname： '/book/bookList.aspx',
search: '?sercate1=001001000',
searchParams： URLSearchParams { 'sercatel' => '001001000' },
hash： '#anchor'
}
url.format(): http://www.gilbut.co.kr/book/bookList.aspx?sercate1=001001000#anchor
```

##### 3.5.7 worker_threads

- 노드에서 멀티 스레드 방식으로 작업하는 방법이다. 먼저 간단한 사용 방법을 알아보자.

```js
const {
    Worker, isMainThread, parentPort,
} = require('worker threads')；
if (isMainThread) { // 부모일 때
    const worker = new Worker(__filename)； 
    worker.on('message', message => console.log('from worker', message))； 
    worker.on('exit', () => console.log('worker exit' ))； worker.postMessage('ping')；
} else { // 워커일 때
    parentPort.on('message', (value) => {
        console.log('from parent', value)；
        parentPort.postMessage('pong')；
        parentPort.close()；
    })；
}
```

- isMainThread를 통해 현재 코드가 메인 스레드(기존에 동작하던 싱글 스레드를 메인 스레드 또는 부모 스레드라고 부름)에서 실행되는지, 아니면 우리가 생성한 워커 스레드에서 실행되는지 구분된다.
- 메인 스레드에서는 new Worker를 통해 현재 파일(__filename)을 워커 스레드에서 실행시키고 있다. 물론 현재 파일의 else 부분만 워커스레드에서 실행된다.
- 부모에서는 워커 생성 후 worker.postMessage로 워커에 데이터를 보낼 수 있다. 워커는 parentPort.on( 'message') 이벤트 리스너로 부모로부터 메시지를 받고, parentPort.postMessage로 부모에게 메시지를 보낸다. 부모는 worker.on( 'message,)로 메시지를 받는다. 참고로 메시지를 한 번만 받고 싶다면 once( 'message')를 사용하면 된다.
- 워커에서 on 메서드를 사용할 때는 직접 워커를 종료해야 한다는 점에 주의하세요. parentPort.close()를 하면 부모와의 연결이 종료된다. 종료될 때는 worker.on('exit')이 실행된다.

콘솔

```
from parent ping
from worker pong
worker exit
```

- 아직까지는 워커 스레드를 사용해 복잡한 작업은 하지 않았다. 이번에는 여러 개의 워커 스레드에 데이터를 넘겨보자. postMessage로 데이터를 보내는 방법과는 다른 방법이다.

<div><img src= "/assets/img/post/main_thread_worker.PNG"></div>

```js
const {
Worker, isMainThread, parentPort, workerData,
} = require('worker—threads')；

if (isMainThread) {
    const threads = new Set()；
    threads.add(new Worker(__filename, {
        workerData： { start： 1 },
    }))；
    threads.add(new Worker(__filename, {
        workerData： { start： 2 },
    }))；
    for (let worker of threads) {
        worker.on('message', message =〉 console.log('from worker', message))； 
        worker.on('exit', () => {
            threads.delete(worker)；
            if (threads.size === 0) {
                console.log('job done')；
            }
        })；
    }
} else { // 워커일 때
    const data = workerData；
    parentPort.postMessage(data.start + 100)；
}
```

- new Worker를 호출할 때 두 번째 인수의 workerData 속성으로 원하는 데이터를 보낼 수 있다. 워커에서는 workerData로 부모로부터 데이터를 받는다. 현재 두 개의 워커가 돌아가고 있으며, 각각 부모로부터 숫자를 받아서 100을 더해 돌려준다. 돌려주는 순간 워커가 종료되어 worker.on('exit’)이 실행된다. 워커 두 개가 모두종료되면 job done이 로깅된다.

콘솔

```
from worker 101
from worker 102
job done
```

- 이번에는 좀 더 실전적인 예제로 소수의 개수를 구하는 작업을 워커 스레드를 통해 해보자. 소수를 찾는 작업은 연산이 많이 들어가는 대표적인 작업이다.
- 먼저 워커 스레드를 사용하지 않으면..?

```js
const min = 2；
const max = 10000000；
const primes = []；
function findPrimes(start, range) {
    let isPrime = true；
    const end = start + range；
    for (let i = start； i 〈 end； i++) {
        for (let j = min； j 〈 Math.sqrt(end)； j++) {
            if (i !== j && i % j === 0) {
                isPrime = false；
                break；
            }
        }
        if (isPrime) {
            primes.push(i)；
        }
        isPrime = true；
    }
}
console.time('prime')；
findPrimes(min, max)；
console.timeEnd('prime')；
console.log(primes.length)；
```

- 2부터 1,000만까지의 숫자 중에 소수가 모두 몇 개 있는지를 알아내는 코드이고, 실행 결과는 아래와 같다.

```
prime： 8.745s
664579
```

- 사용자의 컴퓨터 성능에 따라 다르지만 상당한 시간이 소요된다. 이번에는 워커 스레드를 사용해 여러 개의 스레드가 문제를 나눠서 풀도록 해보자. 미리 말하지만, 멀티 스레딩은 상당히 어렵다. 코드양도 많아진다.

```js
const { Worker, isMainThread, parentPort, workerData } = require('worker_threads')；
const min = 2；
let primes = []；
function findPrimes(start, range) {
    let isPrime = true；
    const end = start + range；
    for (let i = start； i 〈 end； i++) {
        for (let j = min； j 〈 Math.sqrt(end)； j++) {
            if (i !== j && i % j = 0) {
            isPrime = false；
            break；
            }
        }
        if (isPrime) {
            primes.push(i)；
        }
        isPrime = true；
    }
}
if (isMainThread) {
    const max = 10000000;
    const threadCount = 8；
    const threads = new Set()；
    const range = Math.floor((max - min) / threadCount)；
    let start = min；
    console.time('prime')；
    for (let i = 0； i < threadCount - 1； i++) {
        const wStart = start；
        threads.add(new Worker(_filename, { workerData： { start： wStart, range } }))；
        start += range；
    }
    threads.add(new Worker(_filename, { workerData： { start, range： max - start } }))；
    for (let worker of threads) {
        worker.on('error', (err) => {
            throw err；
        })； 
        worker.on('exit', () => {
            threads.delete(worker)；
            if (threads.size === 0) {
                console.timeEnd('prime')；
                console.log(primes.length)；
            }
        })； 
        worker.on('message', (msg) => {
            primes = primes.concat(msg)；
        })；
    }
} else {
    findPrimes(workerData.start, workerData.range)；
    parentPort.postMessage(primes)；
}
```

- 여덟 개의 스레드가 일을 나눠서 처리하게 했다. **멀티 스레딩을 할 때는 일을 나눠서 처리하도록 하는 게 제일 어렵다.** 어떠한 일은 공유하고 있는 데이터가 많아 일을 나누기가 어렵다. 다행히 소수의 개수를 구하는 작업은 정해진 범위(2부터 1000만)를 스레드들이 일정하게 나눠서 수행할 수 있다.

콘솔

```
prime： 1.752s
664579
```

- 속도가 여섯 배 정도 빨라졌다. 워커 스레드를 여덟 개 사용했다고 해서 여덟 배 빨라지는 것은 아니다. **스레드를 생성하고 스레드 사이에서 통신하는 데 상당한 비용이 발생하므로, 이 점을 고려해서 멀티 스레딩을 해야 한다. 잘못하면 멀티 스레딩을 할 때 싱글 스레딩보다 더 느려지는 현상도 발생할 수 있다.**

##### 3.5.8 child_process

- 노드에서 다른 프로그램을 실행하고 싶거나 명령어를 수행하고 싶을 때 사용하는 모듈이다. 이모듈을 통해 다른 언어의 코드(예를 들면. 파이썬)를 실행하고 결괏값을 받을 수 있다. 이름이 child_process(자식 프로세스)인 이유는 현재 노드 프로세스 외에 새로운 프로세스를 띄워서 명령을 수행하고 노드 프로세스에 결과를 알려주기 때문이다.


```js
const exec = require('child_process').exec；
const process = exec('ls')；
process.stdout.on('data', function(data) {
    console.log(data.toString())；
})；// 실행 결과
process.stderr.on('data', function(data) {
    console.error(data. toString())；
})； // 실행 에러
```

- 실행하면 현재 폴더의 파일 목록들이 표시된다. 결과는 stdout(표준출력)과 stderr(표준에러)에 붙여둔 data 이벤트 리스너에 버퍼 형태로 전달된다.

<h3>끝!</h3>