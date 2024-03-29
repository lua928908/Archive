# 타이스크립트 공부하기

<br>

## 타입스크립트란 무엇인가?

[여기](https://www.typescriptlang.org/) 를 누르면 타입스크립트 공식 홈페이지로 이동할 수 있다.
기본적으로 자바스크립트는 1995년 넷스케이프사의 브렌던 아이크가 Navigator2 브라우저에 탑재하기 위하여 만들어진 스크립트 언어이다.

원래 목표가 웹페이지의 보조적인 기능을 수행하기 위함이였고, 초기 브라우저는 서버로 전달받은 html과 css를 렌더링하는 수준이였다. 점차
웹기술이 발전하면서 많은 역할과 기능들을 웹페이지에서 보여주어야 했다.  JavaScript의 가장 큰 논리적 장점은 개발자의 머릿속에서 구현하고 싶은 프로그램이 빠르게 프로토타이핑된다는 것 인데
이런 부분이 규모가 거대해지는 웹 어플리케인을 견고하게 하지 못하는 부분도 있다고 생각한다.
JavaScript 에는 "프로젝트가 진행될수록 JavaScript만으로 작성된 프로그램이 개발자의 의도대로 동작하는 지를 확인하기 힘들다"라는 한계가 있다.
지금 타입스크립트를 학습하고 이 글을 적는 나역시 자바스크립트가 주는 모호함과 이상하다고 생각되는 동작들(호출되는 방식에 따라 this의 값이 변하는 등)에 대해 다른 개발자들도 아쉬움이 있는 것이라고
생각한다.

타입스크립트는 자바스크립트를 확장한 언어로, JS 언어의 특성을 침범하지 않고 최신 ECMA 표준을 지원하고 견고한 웹 어플리케이션을 만들 수 있도록 해준다.
타입스크립트 공식문서에 `TypeScript is Typed Javascript at Any Scale.`이라고 가장먼저 배너영역에 적혀있듯, 타입스크립트는 자바스크립트에 타입이 입혀진 것이다.
이로인해 어떤 장점과 특징들을 가지는지 공부해보자.

<br>

## 타입스크립트의 장점

#### 1. Type 체크 
구글에 검색해보면 타입스크립트를 왜 사용해야 하는지 어떤 장점이 있는지에 대한 많은 글들이 나온다.
그중에도 타입스크립트로 인해 의도치 않은 동작을 방지할 수 있는 한가지 간단한 예제를 살펴보자.

```
let user = {}

function getInfo(){
    axios
        .get(url)
        .then(function (response) {
            response.data;
            username.innerText = user[0].name;
            email.innerText = user[0].email;
            address.innerText = user[0].address;
        })
        .catch(function(e){
            console.error(e)
        })
}
```

만약 axios로 서버에 요청을해 유저정보를 받아와 프론트에 보여주고 싶다면 위와같은 형태로 태그에 정보를 넣어주면 된다.
그런데 만약 username과 email은 단순 string이여서 잘 표현되지만 `user[0].adress`가 객체로 한번더 감싸져 있다면
화면상에서는 `[object object]` 라고 표기되게 될 것이다. 한번쯤 이런 상황을 다들 겪었을 것이다. 이런것 역시 타입스크립트를 사용하므로 인해
예방할 수 있는 버그이다, 최악의 순간은 어플리케이션이 실제 동작하는 과정에서 에러가 발생하는 것이다. 사용자가 실제 버튼을 눌러
API 호출후 버그가 발생하는 경우가 최악의 상황이라는 뜻이다. 그렇기 때문에 typescript코드를 컴파일해 javascript 코드로 변환해 동작을 실행하는 것이
의도치 않는 오류를 방지하는 장점을 주는 것이다, 단지 타입이 안맞는것 뿐만 아니라 값이 있어야 정상적으로 동작하는 경우, 즉 `user` 객체가 값이 존재해야 하는데
아무 값도 없는 `undefined` 상태라면 `user[0].name`을 대입하려고 하는 코드에서도 에러가 발생할 것이다.
타입스크립트를 사용해야만 타입을 지정할 수 있는것은 아니다.
`JSDoc`을 통해 표준문법으로 타입을 지정해 줄 수 있다.
난 JSDoc 이라는게 있는줄 최근에 처음알았다.. 실제 프로젝트에서 쓸것 같진 않은데 관심있다면 [여기](https://jsdoc.app/) 서 자세한 내용을 확인해 보자

<br>

#### 2. 견고함 한스푼 +1
![javascript Spongebob animation image](images/1_opN7wHJG5ITFYXcCvS7LUg.jpeg)

위 이미지를 본적이 있는가?
자바스크립트의 모호함을 잘 알려주는 이미인 것 같다.
개인적으로 자바스크립트를 희안하다고 생각했던 것중 한가지가 this였다 자바스크립트 에서의 this는
함수가 호출되는 방식에 의해 달라진다. 기본적으로 this는 window, 즉 브라우저를 가르키는데
어떠한 객체의 메소드로써 함수가 호출될 때 this는 메서드가 포함된 객체를 가르킨다.
```
const myObj = {
    a: function(){console.log('this = ', this)}, // this = myObj
    b: 'b'
}
myObj.a()
```
위 코드를 살펴보자 `myObj.a`는 a라는 key에 value가 함수이고 그안에서 `this`는 `myObj.a`가 속해있는 `myObj`가 `this`가 된다.

```
const myObj2 = {
  a: function(){
    function innerFunc(){
      console.log('innerFunc this = ', this)
    }
    innerFunc()
  },
  b:'b'
}
myObj2.a()
```

위 코드를 살펴보자 `myObj2.a()`는 `this`가 어떻게 나올까? window가 나오게 된다.
나는 이부분이 조금 이상하다고 생각했다. 사실 함수가 실행되는 상태에 따라 this가 달라진다는 것 자체도 어색했다.
흔히 java 개발자들이 javascript를 배울때 this 때문에 혼란을 겪는 경우가 있다고 하는데, 나는 인생에 처음배운 개발언어가 javascript 였다.
물론 이런 동작이 비논리적이라거나 랜덤스러운 동작이라는 의미는 아니다, 자바스크립트는 규칙에 의해 실행되었고 내부동작을 깊이 파고들다보면
왜 이렇게 동작하는지에 대한 이유가 있겠지만 처음 자바스크립트를 공부할 때 이런 자바스크립트의 특징들이 내겐 많은 의문을 남겼다.
그리고 이런식의 동작이 javascript 언어의 설계상의 문제가 아니냐는 지적이 있는 것으로 알고있다. 현재 자바스크립트 개발을 할 때
함수호이스팅이나 this 때문에 모호한 상황을 만나는 경우는 거의 없을 것이다. 프레임워크 자체가 클로저나 함수호이스팅 같은 상황 때문에 난해함을
겪는 경우가 없도록 잘 만들어 졌기 때문 아닐까 싶다, 어찌되었거나 타입스크립트를 사용하므로 인해 javscript에 더욱 견고함을 더해주었다고 생각한다.
타입스크립트를 사용하므로 인해 어떤 타입을 받을 수 있는지 어떤 타입을 인자로 넘겨주어야 하는지 객체를 사용할 때에는 어떤 프로퍼티들이 있었는지 짧은 순간이나마
기억하려 애쓰지않아도 IDE의 `Code completion` 기능을 통해 더욱 편하게 사용할 수 있는 것이다.
vs code 이러한 자동완성 기능을 `Intellisense` 라고 표현하는 것 같다. 이런 자동완성 기능이 별 것 아닌것 같지만

<br>

## 타입스크립트 프로젝트 시작하기 

그래서 타입스크립트를 시작하려면 어떻게 해야할까?
타입스크립트는 언급했듯 컴파일을 해서 자바스크립트 파일로 변환되어 브라우저에서 동작하게 된다.
```
npm i typescript -g
```
npm 에서 타입스크립트를 gloal로 설치하자, `-g`를 붙여주면 노드패키지 매니져인 npm을 통해 타입스크립트를 지역설치가 아닌
전역설치 할 수 있다.

```
tsc index.ts
```
위 코드처럼 tsc를 하면 tx파일을 js파일로 변환할 수 있다. 마치 `javac`를 통해 자바파일을 class파일로 컴파일 하는것과 비슷하다.

하지만 매번 많은 ts파일을 일일이 컴파일 해줄 수는 없다. 그렇기 때문에 웹팩을 사용하거나
백엔드의 경우 `tsconfig.json` 를 통해 key:vlaue 의 형태로 타입스크립트 옵션을 설정해 줄 수 있다.
컴파일할때의 디렉토리 위치나 옵션들을 정의하는 것이다.

```
{
    "compilerOptions": {
        "allowJs": "true", // 이 프로젝트에 자바스크립트를 허용 여부
        "checkJs": true, // .js 파일에 유효성을 검사한다, ts-ignore로 제외 가능
        "noImplicitAny": true // 모든타입을 정의할 때 최소 any라도 정의해야 한다
    }
}
```

이러한 형태로 사용되는데 최소한 저정도 옵션3가지는 기재가 되게된다.
공식홈페이지의 tools 라는 메뉴에서 설정관련 레퍼런스를 확인할 수 있다, [여기](https://www.typescriptlang.org/tsconfig) 를 눌러 확인해보자


## 타입스크립트 문법 살펴보기


#### 타입의 종류

타입스크립트에는 12가지의 타입이 있다.

------------
| 타입 | 설명 | Ex) |
| :------ | --- | --- |
| `Boolean` | true/false | `const type: boolean = true` |
| `Number` | 숫자 | `const type: number = 10` |
| `String` | 배열 | `const type: Array<number> = [1,2,3]` |
| `Object` | object라고 타입을 선언하는것 만해도 되지만 내부 타입까지 지정해줄 수 있다. | `const type: object = {a: string, b: number} = {a: 'im korean', b: 0815}` |
| `Array` | 배열 | `const type: boolean = true` |
| `Tuple` | 배열인데 특정index에 type까지 지정할 수 있다. | `const type: Array<string, number> = ['hi', 9]` |
| `Enum` | 특정값 (상수)의 집합, Atype은 인터페이스 이다. | `const type: enum = {Atype, Btype, Ctype}` |
| `Any` | 무엇이든 가능 | `const type: any = 'everything'` |
| `Void` | 변수에선 null/undefined를 함수에선 리턴값이 없음을 의미한다. | `function example(): void { console.log('hi~') }` |
| `Null` | 의도된 없음 | `const type: boolean = true` |
| `Undefined` | 의도하지 않은 없음 | `const type: boolean = true` |
| `Never` | 함수의 끝에 절대 도달하지 않는다는 의미 | `function myFunc(): never{ while(true) {console.log('loop')} }` |

특이하게 배열을 정의할때는 앞글자를 대문자인 Array로 적어주어야 한다. 일관성 있게 통일되면 좋은텐데 아쉽다.

<br>

#### 파라미터 정의

```
function sum(a: number, b: number): number{
    return a+b
}
sum(1, 2)
```
함수를 정의해 줄 때 파라미터는 위 코드처럼 사용할 수 있다, 그리고 맨위 가장오른쪽 number 처럼
반환값을 지정해 줄 수 있다.
너무 단순한 내용 이지만 이런 타입정의가 개발자입장에서 타입을 추론하는데 도움이 된다.
만약 의도치않게 sum 함수를 사용할 때 인자를 더 많이 넘겨주면 에러가 발생한다.
js에서는 `arguments`라고하는 약속된 내장파라미터(?)를 가지고 함수가 받아온 인자를 모두 가져올 수도있지만
타입스크립트에서는 애초에 스펙과 다르게 사용되었기 떄문에 에러로 인식되고
자바스크립트의 유연함을 잃기는 하지만 개발자 입장에서 이정도의 귀찮음(?)은 귀찮음으로 인식하면 되는것 같다.

하지만 함수에 파라미터가 필수가 아닐수도 있다.

```
function log(a: string, b?: string){
    
}
```

log 함수를 사용할 때 로깅하고 싶은 값이 1개일수 있지만 여러개일 수도있을 것이다.
이럴때를 위해 옵셔널 파라미터로 정의할 수 있도록 물음표를 붙여 선택사항으로 만들 수 있다.
즉 log함수에서 b 라고 정의되어있는 파라미터는 사용하지 않을수 있는 것이다.

나는 물음표를 사용해 옵셔널로 만드는것을 `style-components`를 사용할 때 처음 보게되었는데 타입스크립트에서도 사용하는 것을보며
무엇인가를 배우다보면 다른 영역에서도 전에 배웠던 지식이 도움이 될 때가 있는것 같다.

<br>

여기까지는 별도의 설명이나 큰 비용없이 가이드를 조금만 일거보면 충분히 알 수 있을것 같은 내용이다.

그런데 한가지 기억할만한 부분은 인자로 받는 값이 object이며 object안에 특정 프로퍼티를 삽입하는 경우 object의 내부 shape를 구체적으로 지정해 주는것이 좋다.
말로 설명하면 복잡해 보이지만 단순하다.

```
const todos = [
    { id: 1, title: '할일 01', done: false },
    { id: 2, title: '할일 02', done: true },
    { id: 3, title: '할일 03', done: false },
];

function showCompleted() {
  return todoItems.filter(item => item.done);
}
```
예를들어 ToDo List에서 할일을 완료된 리스트만 필터링한다고 할 때 타입스크립트를 적용하지 않는다면
위 코드와 같은 모습일 것이다.

```
const todos: object[] = [
    { id: 1, title: '할일 01', done: false },
    { id: 2, title: '할일 02', done: true },
    { id: 3, title: '할일 03', done: false },
];

function showCompleted(): object[] {
  return todoItems.filter(item => item.done);
}
```
위 코드처럼 return 값이 배열이고 그안에 object가 들어간다고 표기해 주었다.
하지만 실제로 IDE 에서는 에러를 표기하게된다. 왜냐면 `showCompleted` 함수 내부에서 `item.done`를
사용하게 되는데 item 이라는 object가 프로퍼티로 done 이라는 key를 가지고 있는지 알 수 없기 때문이다.

```
const todos: {id: number; title: string; done: boolean}[] = [
    { id: 1, title: '할일 01', done: false },
    { id: 2, title: '할일 02', done: true },
    { id: 3, title: '할일 03', done: false },
];

function showCompleted(): {id: number; title: string; done: boolean}[] {
  return todoItems.filter(item => item.done);
}
```
그렇기 때문에 위 예제처럼 object 라고 적었던 부분을 구체적으로 object의 shape를 적어주면 에러가 해결된다.
기억할 부분은 오브젝트를 명시하듯이 콤마(,)로 구분하는것이 아니라 세미콜론(;)으로 구분해주고 마치 for문의 조건식을 작성하듯
마지막 부분은 세미콜론(;)을 적지 않는다는 것이다.

<br> 

## 인터페이스

타입스크립트에서는 인터페이스의 개념이 있다.
자바처럼 인터페이스를 만들 수 있다.

```
interface User {
    age: number;
    name: string;
}

const taylor: User = {
    age: 30,
    name: 'taylor'
}
```
위 처럼 유저라는 인터페이스를 만들게 되면 어떠한 변수나 함수의 리턴값에 타입을 인터페이스로 정의해 줄 수 있다.
그리고 변수의 타입이나 파라미터로 받은 인터페이스를 기반으로 타입이 맞지않는 경우나 없는 프로퍼티를 사용하는 경우
에러를 보여주며 타입으로 인한 오류를 사전에 방지해 주는 것이다.

<br>

```
interface User {
    age: number;
    name: string;
}

interface TempUser extends Partial<User> {
    expiration: Date
}
```

위 코드를 살펴보자 `User` 라는 인터페이스를 상속하는 `TempUser` 를 만들었다.
임시로 활성화되는 사용자를 상상하며 아주 짤막하게 만들어본 예제이다.

중요한 부분은 `Partial` 이라는 키워드 이다. 타입스크립트에는 유틸리티 타입이 있고,
상속을 할 때 조건부로 상속하게 하는 등 타입과 관련한 유틸리티를 제공한다.

위 예제에서 Partial은 상속받은 인터페이스의 프로퍼티를 옵셔널로 바꿔준다. 즉 있어도 되고 없어도 되는 타입으로
변경해 주는 것이다.

```
interface TempUser {
    age?: number
    name?: string
    expiration
}
```
위 처럼 `age`와 `name` 프로퍼티는 있을수도 있고 없을수도 있는 옵셔널이 되는 것이다.
이렇게 인터페이스로 만들어 타입을 관리하는것은 추후에 어플리케이션이 복잡해졌을 때 훨씬더 체계적이고
견고한 프로그래밍이 가능하게 해준다.

<br>

#### 인터페이스 함수 정의

```
// 인터페이스로 함수의 스펙을 정의
interface SumFunction {
    (a: number, b: number): number
}

let sum: SumFunction
sum = function(a: number, b: number): number {
    return a+b
}
```
이렇게 함수의 스펙을 정의하는 것도 가능하다.
라이브러리를 개발하거나 많은 인원이 함께 작업할 때 더욱 큰 효옹을 줄 것이다.

#### 인터페이스 인덱싱 정의

```
interface StringArray {
    [index: number]: string;
}

const arr = StringArray = ['a', 'b', 'c']
```

위 예제를 살펴보자 나는 처음 타입스크립트로 만들어진 프로젝트를 살펴볼 때
대부분 쉽게 이해가 되었고 직관적이었는데 위 처럼 생긴 모양에 코드가
굉장히 헷갈렸다.

우선 `[index: number]` 이 부분은 배열안에 들어가는 index가 number 타입이여야 한다는 의미이고
오른쪽에 `string`은 배열안에 들어가는 실제 값이 문자열이 어이야 한다는 의미이다
즉, `const arr = StringArray = ['a', 'b', 'c']` 이런 모양으로 StringArray 타입을 만들어야 하는 것이다.
배열에 index는 원래 숫자밖에 들어갈 수 없어서 저게 무슨 의미인가 하는게 첫번째로 헷갈렸고 index의 이름을 `index`로 지어서
더욱 헷갈렸다.

위에 경우 인터페이스 자체의 이름이 StringArray 여서 오해할 수 있겠지만
저런 형태의 인터페이스가 오브젝트에서 똑같이 적용된다.

```
interface TestArr {
    [key: number]: string
}
const arr: TestArr = ['a', 'b', 'c']
console.log('arr = ', arr)


interface TestObj {
    [key: string]: number
}
const obj: TestObj = {a: 1, b: 2, c: 3}
console.log('obj = ', obj)
```

이해가 되는가 걱정스럽다, 결과적으로 인덕셍 방식을 정의하는 인터페이스 를 사용해서
배열의 `인덱싱+값` 타입을 정의하거나, 오브젝트의 `인덱싱+값` 타입을 정의하는 것이 가능한 것이다.
그런데 나는 처음 이 개념을 알아갈 때 `[key: number]` 이렇게 배열로 감싸져 있어서 오브젝트는 `{key: string}` 이런 모습으로
정의해야 한다고 생각했었다.

<br>

## 타입 별칭

타입스크립트에는 타입 별칭이라는 방식의 타입을 정의하는 방법이 있다.

```
type Person = {
    name: string
    age: number
}

const taylor: Person = {
    name: '테일러',
    age: 30
}
```

이런 형태로 인터페이스와 매우 비슷하게 작성된다. 타입별칭은 타입을 사용하는 모든곳에서 사용될 수 있다
타입별칭과 인터페이스의 가장 큰 차이는 타입별칭으로 작성된 타입은 마우스를 올리면 어떤 형태로 타입이 구성되어 있는지
볼 수 있다. 즉, `const Taylor: Person = {}` 부분을 작성할 때 어떤 프로퍼티들을 정의해주어야 하는지 IDE에서 마우스만 올려도 바로 확인해 볼 수 있다.

<br>

그렇다면 타입별칭과 인터페이스는 어떤차이가 있을까?
타입 별칭은 말그대로 타입에 별칭을 주는것이다, 새로운 타입을 만드는 것이 아니라 `{name: string, age: number}`와 같은 형태로 객체의 타입을
명시하고 나중에 재사용하기 편하게 별칭을 적어주는 것 뿐이다. 무엇보다 가장큰 차이는 타입의 확징이 불가능 하다는 점이다.
타입스크립트는 규모가 커지는 어플리케이션에 견고함을 주는 장점이 있다고 했는데 타입별칭은 확장이 안된다는 문제가 있다.
그렇기에 인터페이스를 사용해 타입을 명시해주고 모듈로 import해 사용하는 것이 좋다.

<br>

## 클래스

```
class Person {
    private name: string
    public age: number
    readonly log: string

    constructor(anme: string, age: number){
        this.name = name
        this.age = age
    }
}
```
ES6(ES 2015) 문법을 사용해봤던 사람이라면 크게 어렵지 않을것이다.
타입스크립트에서의 클래스를 선언할 경우 기본적으로 멤버변수를 다 선언해주고 타입도 지정해야 한다.
그리고 `private` 와 `public` 키워드를 통해 자바처럼 멤버변수의 유효범위를 설정할 수 있고
`readonly`와 같은 키워드를 통해 읽는것만 가능하게 만들수도 있다.

<br>

## 제네릭

제네릭은 C#, JAVA등의 언어에서 재사용성을 높이기위해 사용한다.
나는 자바를 잘 모르지만 제네릭을 처음 배울때 개념이 낯설고 왜쓰는건지 참 이해가 안되었다.
기본적으로 제네릭은 어떤 클래스나 변수를 정의하거나 사용할 때 타입을 지정하는 것이다.

```
function logText(text){
    console.log(text)
    return texxt
}

logText(10)
logText('hi')
logText(true)
```
파라미터로 받은 값을 로그로 출력하는 단순한 내용이다. 그런데 지금 위에 예제는 타입이 무엇이든 상관없이 받고있다.
만약 자바였다면 숫자, 문자, 혹은 불리언 값을 받아 반환하는 logText메서드를 각각 만들어 주어야 할 것이다.

```
function logText<T>(text: T): T{
    console.log(text)
    return text
}

logText<string>('hllo')
logText<number>(2020)
logText<boolean>(true)
```
이번엔 제네릭을 적용하였다. logText를 호출할 때 타입을 넘겨주면 선언했던 함수의 타입에 매칭되어 적용되는 것이다.
T 라고 정의하는 이유는 관용적으로 type을 의미하는 T라고 표기하기 때문이다. T가아니라 S나 W ABCD 라고 적어도 상관없다.
for문을 작성할 때 초기값을 i로 표기하는 것과 같은 맥락이다. 관용적으로 그렇게 쓰였다.

제네릭이 좋은 이유는 유니온 타입으로 파라미터를 받는경우 공통되는 프로퍼티혹은 공통되는 메서드만 사용할 수 있게된다.
하지만 제네릭은 아예 타입자체를 사용할 때 정의하는 것이기 때문에 반환값을 받아 특정 타입만 사용할 수 있는 메서드를 사용하는 것도 가능하다.

<br>

```
interface Person<T> {
    name: string
    job: T
}

const person : Person<number> = {name: 'taylor', job: 10}
```

인터페이스를 선언할 때 제네릭으로 타입을 정의하고 싶은경우 위 예제처럼 사용하면 된다.
클래스도 마찬가지 이다,

#### 제네릭 타입 제한하기

```
function logText<T>(text: T): T{
    console.log(text.length)
    return text
}
```
예제처럼 정의하면 에럭 발생한다. logText에 T라는 제네릭이 무엇으로 올지 모르기 때문에
length 라고하는 속성을 사용할 수 없는 것이다.

```
interFace LengthType {
    length: number
}

function logText<T extends LengthType>(text: T): T{
    console.log(text.length)
    return text
}

logText('hello')
```
다소 헷갈릴수 있는데 위 예제처럼 제네릭에 상속을 사용하면 `logText`를 호출할 때 인자로 받는 값에는
항상 length라는 프로퍼티가 포함되어야 한다. length의 타입이 숫자인 것이지 받는값이 숫자여야 하는것은 아니다.
`logText('hello')` 이렇게 호출하게 되면 hello 라는 문자열은 length라는 속성을 가지고 있고 'hello'.length는 리턴값이
숫자이므로 사용할 수 있는 것이다.

<br>
