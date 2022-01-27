# async/await

## async/await 이란?

지금까지 비동기/동기의 의미와 왜 비동기 처리가 필요한지 등등을 간략하게 알아봤다, 그런데
ECMAScript 2017 에서 async 함수가 추가되었다. 콜백헬을 해결해주던 Promise에서 더욱 가독성을 높이고
사람의 입장에서 더욱 일기 쉽도록 만들어 준다.

<br>

```
function hello() { return "Hello" };
hello();
```

웹 브라우저 콘솔에 위 코드를 실행해 보자, 쉽다 Heollo를 반환한다

```
async function hello() { return "Hello" };
hello();
```
위 코드는 함수 앞에 async라는 키워드가 붙었다, 실행해 보면

![콘솔결과](../images/스크린샷%202021-07-28%20오후%209.42.50.png)

이런 모습이다, Promise를 반환했다 그렇다 async 키워드를 붙이면 함수가 그냥 값을 return 하는게 아니라 Promise를 반환하게 된다.
Promise를 반환했으니까 .then을 붙여서 return받은 값을 활용할 수 있다.
```
async function hello() { return "Hello" };
hello().then((result) => {
    console.log('result = ', result)
})
```

<br>

async 키워드를 붙여 함수가 Promise를 반환하게 했다면 await 키워드를 붙여
해당 함수가 값을 반환할 때 까지 기다릴 수 있습니다, async/await을 사용하는 경우
필수적으로 try/catch를 붙여주는 것이 좋습니다.

```
let myFunc = async () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(2)
      resolve()
    }, 2000)
  })
}

console.log(1)
await myFunc()
console.log(3)
```

맨 처음 나왔던 예제와 비슷하다, 이렇게 await 키워드를 붙여 동기처럼 동작하게 할 수 있다.

```
async function myFetch() {
  let response = await fetch('coffee.jpg');
  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }
  return await response.blob();

}

myFetch().then((blob) => {
  let objectURL = URL.createObjectURL(blob);
  let image = document.createElement('img');
  image.src = objectURL;
  document.body.appendChild(image);
})
.catch((e) =>
  console.log(e)
);
```

위 코드를 확인해 보자 만약 이미지를 먼저 불러온 이후에 어떠한 처리를 해주어야 한다면
`myFetch()` 라는 async 함수를 만들고 `.then`으로 콜백처럼 다음 코드를 실행하게 할 수 있다.

## await는 최상위 레벨에서 작동하지 않는다.

```
let response = await fetch('/article/promise-chaining/user.json')
let user = await response.json()
```

최상위 레벨에서 await을 사용하려고 하면 문법 에러가 발생한다.

```
(async () => {
let response = await fetch('/article/promise-chaining/user.json');
let user = await response.json();
...
})();
```
하지만 익명함수로 감싸주게 되면 최상위 레벨에서도 await을 사용하는 것이 가능해 진다.
상당히 유용하다고 생각이 든다. 잘 기억해 놓자.

<br>

## async/await 이 Promise의 대체제 인가?

MDN Web docs 에서 예제를 가져와 봤다.
https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise

HTML
```
<button id="btn">프로미스 만들기!</button>
<div id="log"></div>
```

javascript
```
'use strict';
var promiseCount = 0;

function testPromise() {
var thisPromiseCount = ++promiseCount;

var log = document.getElementById('log');
log.insertAdjacentHTML('beforeend', thisPromiseCount +
') 시작 (<small>동기적 코드 시작</small>)<br/>');

// 새 프로미스 생성 - 프로미스의 생성 순서를 전달하겠다는 약속을 함 (3초 기다린 후)
var p1 = new Promise(
// 실행 함수는 프로미스를 이행(resolve)하거나
// 거부(reject)할 수 있음
function(resolve, reject) {
log.insertAdjacentHTML('beforeend', thisPromiseCount +
') 프로미스 시작 (<small>비동기적 코드 시작</small>)<br/>');
// setTimeout은 비동기적 코드를 만드는 예제에 불과
window.setTimeout(
function() {
// 프로미스 이행 !
resolve(thisPromiseCount);
}, Math.random() * 2000 + 1000);
}
);

// 프로미스를 이행했을 때 할 일은 then() 호출로 정의하고,
// 거부됐을 때 할 일은 catch() 호출로 정의
p1.then(
// 이행 값 기록
function(val) {
log.insertAdjacentHTML('beforeend', val +
') 프로미스 이행 (<small>비동기적 코드 종료</small>)<br/>');
})
.catch(
// 거부 이유 기록
function(reason) {
console.log('여기서 거부된 프로미스(' + reason + ')를 처리하세요.');
});

log.insertAdjacentHTML('beforeend', thisPromiseCount +
') 프로미스 생성 (<small>동기적 코드 종료</small>)<br/>');
}

if ("Promise" in window) {
var btn = document.getElementById("btn");
btn.addEventListener("click", testPromise);
} else {
log = document.getElementById('log');
log.innerHTML = "Live example not available as your browser doesn't support the <code>Promise<code> interface.";
}
```

<br>

#### 왜 이런 처리가 필요한가?

실제 개발을 할 때는 이런 기능을 어디에서 사용할까?
주로 이미지 같은 리소스를 받아와야 하거나, 혹은 로그인이 처리된 이후에 동작되어야 하는 경우 처럼 사전에 어떠한 동작이 처리되어야 하는 경우 사용되고
실제 개발을 하다보면 동기적으로 순서가 보장되어야 하는 경우가 많이 있다.

#### Promise.all() 메서드

배열을 반복하는 map() 같은 메서드 내부에서 .then 혹은 await을 사용하고 싶은 경우가 있다, 이런경우
for of 문을 사용하거나 Promise.all() 메서드를 사용해야 한다.
```
const array = ['a','b','c','d']

// await 키워드를 사용해도 동기적으로 동작하지 않는다.
array.map((value) => {
await someMathod(value)
})

// for-of 문을 사용하거나
for(const value of array){
await someMathod(value)
}

// Promise.all()을 사용해야 한다.
await Promise.all(
array.map((value) => {
await someMathod(value)
})
)
```
위 코드처럼 for-of를 사용하거나 Promise.all()을 사용해야 한다. 최대한 짧게 표현하기 위해
무의미한 array나 someMathod로 표현했다, Promise.all() 메서드는 파라미터로 전달된 모든 프로미스가 fulfilled 상태가 될 때 까지 다른 코드를 실행하지 않는다.

<br>

[여기](https://medium.com/@constell99/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-async-await-%EA%B0%80-promises%EB%A5%BC-%EC%82%AC%EB%9D%BC%EC%A7%80%EA%B2%8C-%EB%A7%8C%EB%93%A4-%EC%88%98-%EC%9E%88%EB%8A%94-6%EA%B0%80%EC%A7%80-%EC%9D%B4%EC%9C%A0-c5fe0add656c) 글을보면 4~5년 전 글이긴 하지만
async/await 이 promise를 대체하는 신기능 쯤으로 생각하는 것 같다. 내 짧은 지식이 잘못 판단하는 걸짐 모르지만 async/await이 proimse를 대체하기 위한
것은 아니라고 생각된다. 결국 async 함수가 Promise를 반환하는 것이니까 사실 대체 한다는 것은 이상한 말인 것 같다.

```
let myFunc = async () => {
    setTimeout(() => {
        console.log(2)
        return 2
    }, 2000)
}

console.log(1)
await myFunc()
console.log(3)
```
위 코드는 1,3,2가 콘솔에 출력된다 myFunc라는 함수가 콘솔을 출력한 이후 return을 하지만
위 방법으로는 우리가 원하는 대로 동기로 동작하게 할 수 없다.

```
let myFunc = async () => {
    return new Promise((resolve, reject) => {
            setTimeout(() => {
                console.log(2)
            resolve()
        }, 2000)
    })
}

console.log(1)
await myFunc()
console.log(3)
```
동기로 사용하고자 하는 코드가 Promise로 만들어져 있어야 동기적으로 실행하는 것이 가능하다.
실무에 개발하며 별 공수없이 await을 사용하는 라이브러리들도 모두 프로미스를 지원하기 때문에 이러한 사용이 가능한 것이다.