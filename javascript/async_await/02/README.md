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

![](../images/스크린샷%202021-07-28%20오후%209.42.50.png)

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