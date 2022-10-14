# SpringBoot + Next.js 프로젝트 회고

> 이 글에서는 현재 회사에서 Next.js + SpringBoot +  JPA 로 진행하는 프로젝트를 개발해 가면서 몰랐던 새로 알게되는 전반적인 내용들을 기록하려 한다.
회사에서 진행하는 프로젝트이다 보니 예제코드는 example로 변경해서 기록되어 있기에 정확하지 않을 수 있다.
> 
> 잘못된 설명이나 오타에 대한 pull request 늘 환영 합니다. 


---

# 쿠키 발급이 안되는 문제

## 시나리오
1. 기존에는 accessToken과 refreshToken을 localStorage에 보관했었다.
2. 알고보니 token을 localStorage 같은 곳에 보관하면 확장프로그램이나 기타 XSS공격과 같은 심각한 보안문제가 발생한다고 한다.
3. 결과적으로 refreshToken은 HTTP 세션에 보관하고 accessToken은 redux 같은 메모리에만 보관하기로 했다.
4. Header 컴포넌트 useEffect에서 refreshToken을 가지고 accessToken을 발급받는 로직을 실행시킨다. (refreshToken은 자동으로 붙임)
5. 로컬에서 프론트와 백엔드를 모두 띄우고 로그인을 하면 쿠키가 정상적으로 발급된다.
6. 로컬 프론트에서 서버에 올라가있는 백엔드로 로그인을 하면 로그인은 성공하지만 쿠키발급이 안되는 문제가 있다.

![발급된 쿠키에 경고 메세지](images/image01.png)

위 이미지처럼 쿠키에 문제가 있다는 오류가 발생.

<br>

## 해결법

나의 상황에서는 쿠키를 정상적으로 발급 하려면  둘중 한가지에는 해당되어야 한다. (로컬에서 서버로 보내는 경우)
- secure가 true 이면서 SamSite 속성이 none인 경우 (SamSite가 none이여도 보인정책에 따라 기본적으로 Lax로 된다.)
- secure가 false 이면서 SamSite 속성이 설정되어 있고 도메인이 같은 경우
  그런데 나의 상황에서는 로컬에서 서버로 보내는 것이기 때문에 https로 보내기 까다롭다 그래서 secure는 무조건 false여야 하기 때문에
  로그인을 요청하는 도메인과 처리하고 쿠키를 발급하는 도메인이 같아야 한다.

결국 로그인 요청 도메인과 인증/쿠키를 발급하는 도메인이 다르기 때문에 발생하는 문제다.
우리는 hosts파일을 변경해 cookie.com이 server.cookie.com에게 로그인 요청을 보내고 쿠키를 발급하게 했다.

```
# /etc/hosts 설정

##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost
# Added by Docker Desktop
# To allow the same kube context to work on the host and the container:
127.0.0.1 kubernetes.docker.internal
# End of section

127.0.0.1 cookie.com
192.168.0.114 server.cookie.com
```


위 예시처럼 hosts파일을 변경했다. 프론트는 cookie.com:3000으로 접속해 next.js 어플리케이션을 통해 server.cookie.com에게 axios 로그인 요청을 보낸다. 그럼 도메인이 동일하기 때문에 쿠키가 정상 발급 된다.

대부분의 블로그나 stackoverflow에 이런 내용이 안나와 있는 이유는 대부분의 사람들이 localhost에서 프론트와 백엔드를 모두 띄웠을 것이기 때문이다.

.env 파일도 수정해주자 프론트가 server.cookie.com한테 axios 요청을 보내면 hosts 설정에 따라 내부 백엔드 서버가 띄워져있는192.168.0.114(옆자리 개발자분 맥북) 로 요청이 가게 된다.

<br>

### next 프로젝트 .env  설정
```
NEXT_PUBLIC_API_URL=http://server.cookie.com:8888
NEXT_PRODUCTION_URL=https://example.com
```

### 스프링부트 프로젝트 TokenUtil.java 설정
```java
public static void setAuthCookie(String refreshToken, HttpServletResponse httpResponse) {
  Cookie cookie = new Cookie("refreshToken", refreshToken);
  cookie.setHttpOnly(true);
  cookie.setDomain("flab-dev.net");
  cookie.setSecure(true);
  cookie.setPath("/");
  httpResponse.addCookie(cookie);
}
```

![정상적으로 발급/저장된 쿠키](images/image02.png)

위 이미지처럼 정상적으로 쿠키가 발급 되었다.

---

# getServersideProps 요청시 state 초기화

getServersideProps (SSR) 처리시 HYDERATE 액션 타입이 state를 초기화 하는 문제
getServerSideProps를 호출할 때 즉 SSR처리를 하면 서버에 있는 스토어인스턴스가 `HYDRATE` 타입의 액션을 호출한다.

## 시나리오
1. 로그인 화면에서 로그인을 한다
2. redux-saga를 통해 redux 스토어에 firebaseIdToken 이라는 값이 저장한다.
3. 다른 페이지로 이동한다. (예를들어 공지사항)
4. 공지사항 페이지에서(/pages) getServerSideProps를 이용해 SSR로 데이터를 가져온다.
5. `HYDRATE` 타입의 액션이 실행된다.
6. 초기값으로 state가 덮어씌어 지는 오류가 발생한다.

<br>

기존에는
```javascript
case HYDRATE:
    return action.payload
```
이런 식으로 payload를 리턴하게 되었는데 payload안에 initialState가 들어있으므로 초기화가 되어버린다.
firebaseIdToken도 null 값으로 초기화를 하기 때문에 refreshToken이 있는 사용자 인데도 없는 것으로 잘못 처리하게 된다.

```
case HYDRATE:
      action.payload.member.firebaseIdToken = state.member.firebaseIdToken
      const nextState = {
        ...state,
        ...action.payload
      }
      return nextState
```
위 상태처럼 HYDRTE 타입의 액션이 실행될 때 firebaseIdToken을 무조건 클라이언트의 상태에서 사용하게 임시방편 하였음.
결과적으로는 클라이언트의 state와 서버의 state를 합치는 처리가 있어야 할 듯 함.

아래는 123에 나와있는 예제이다.
아래 예제에서도 overwrite되니 주의하라는 주석이 있다.
```
// store.ts

import {createStore, AnyAction, Store} from 'redux';
import {createWrapper, Context, HYDRATE} from 'next-redux-wrapper';

export interface State {
  tick: string;
}

// create your reducer
const reducer = (state: State = {tick: 'init'}, action: AnyAction) => {
  switch (action.type) {
    case HYDRATE:
      // Attention! This will overwrite client state! Real apps should use proper reconciliation.
      return {...state, ...action.payload};
    case 'TICK':
      return {...state, tick: action.payload};
    default:
      return state;
  }
};

// create a makeStore function
const makeStore = (context: Context) => createStore(reducer);

// export an assembled wrapper
export const wrapper = createWrapper<Store<State>>(makeStore, {debug: true});
```

원글: https://github.com/kirill-konshin/next-redux-wrapper#usage

## 해결법
```javascript
const rootReducer = (state: any, action: any) => {
  switch (action.type) {
    case HYDRATE:
      action.payload.member.firebaseIdToken = state.member.firebaseIdToken
      const nextState = {
        ...state,
        ...action.payload
      }
      return nextState
    default: {
      const combineReducer = combineReducers({
        test,
        mypage,
        comment,
        member,
        question,
        news,
        tech
      })
      return combineReducer(state, action)
    }
  }
}
```
위에서 언급하듯 임시방편으로 state에 있는 firebaseIdToken을 payload에 firebaseIdToken에 넣어주었다.
이 방법으로도 되긴 하지만 뭔가 그리 좋은 방법은 아닌 듯 하다. 혹시 나중에 서버측에서 firebaseIdToken을 새로 받아와야 하는 상황이 생길수도 있지 않을까.

<br>

세가지 정도 방법이 있는 것 같다.
1. overwrite 되면 안되는 값만 클라이언트 값으로 사용하기 (위에 예시처럼)
2. state와 action.payload 값을 비교해 있는값이 들어가게 처리하기 (`json-merge` 같은 라이브러리로 적절히 처리하기)
3. redux store에 auth라는 별도의 key를 만들어 auth라는 값은 무조건 클라이언트 값으로 사용하기

3번 방법은 1번 방법과 비슷한데 firebaseIdToken 이라는 하나의 프로퍼티만 지정해서 관리해주기보다 auth라는 객체 자체를 무조건
클라이언트 측 상태를 쓰게 하는 방법이다.

### 2022.10.14 추가
> auth라는 별도의 state를 만들어서 인증과 관련된 부분은 auth에서 관리하였다.

<br>
---
<br>

# CSR/SSR 서로 상태가 호환안되는 문제 (인증 상태 유지하기 해결하기)
## 시나리오

1. 로그인을 한다 (메인페이지로 이동)
2. `axios.defaults.headers.common.firebaseIdToken = 'ABCD...'` 을 추가한다. (refreshToken은 쿠키에 idToken은 메모리상에서만 보관하기로 함, localStorage 안됨)
3. 다른 페이지로 이동
4. 이동한 페이지의 `serverSideProps`에서 dispatch하는 axios는 `firebaseIdToken`이 없다 (로그인 후 설정해주었는 데도 없다)

처음 이 문제를 확인했을 때 axios의 instance가 서로 달라서 그런건가 라고 생각했다. 그래서 아래 코드처럼 `axiosInstance`라는 인스턴스를 만들어 axios요청이 필요한 곳에서 import해 사용했었다.

```javascript
const axiosInstance = axios.create()
axiosInstance.defaults.baseURL = process.env.NEXT_PUBLIC_API_URL
axiosInstance.defaults.headers.common['Content-Type'] = 'application/json'
axiosInstance.defaults.withCredentials = true

// refreshToken 관련 코드
axiosInstance.interceptors.response.use((response) => response, errorHandler)
```
그런데 이렇게 싱글톤으로 인스턴스를 가져와 사용하게 만들어도 문제가 계속되자 구글링을 하다 보니 CSR/SSR 서로 각각 상태공유가 안되는 것으로 보였다.
어떤 글에서는 axios를 사용하지 말고 create-next-app을 하면 생성되는 `/api` 디렉토리에 있는 예제처럼 api를 사용하라고 하는 글도 있었다.

<br>

api처리를 변경하는 것은 무리가 있다고 생각되어서 찾다가 결과적으로 처리한 방식은 _app.tsx에 서버사이드랜더링 부분에 cookie를 커리하는 코드를 넣는 방법 이였다.
```javascript
App.getInitialProps = async (context: any) => {
  const {ctx, Component} = context
  let pageProps: any = {}
  const allCookies = cookies(ctx)

  if (allCookies && allCookies.refreshToken) {
    axiosInstance.defaults.baseURL = process.env.NEXT_PUBLIC_API_URL
    axiosInstance.defaults.headers.common['Content-Type'] = 'application/json'
    axiosInstance.defaults.withCredentials = true
    axiosInstance.defaults.headers.common['Cookie'] = `refreshToken=${allCookies.refreshToken}`
    const {data} = await axiosInstance({
      method: 'post',
      url: '/refresh-token',
      withCredentials: true
    })
    axiosInstance.defaults.headers.common.firebaseIdToken = data.data.firebaseIdToken
    pageProps.firebaseIdToken = data.data.firebaseIdToken
  } else {
    axiosInstance.defaults.baseURL = process.env.NEXT_PUBLIC_API_URL
    axiosInstance.defaults.headers.common['Content-Type'] = 'application/json'
    axiosInstance.defaults.withCredentials = true
    pageProps.firebaseIdToken = null
    delete axiosInstance.defaults.headers.common['Cookie']
    delete axiosInstance.defaults.headers.common.firebaseIdToken
  }

  if (Component.getInitialProps) {
    pageProps = await Component.getInitialProps(ctx)
  }

  return {pageProps}
}
```

* `_app.tsx`에서는 `getServerSideProps`가 동작하지 않는듯 하다
* 다른 페이지에서는 `getServerSideProps`를 할 때 `next-redux-wrapper`를 통해 만든 wrapper를 가져와 사용하지만 최신 next버전 에서는 `next-redux-wrapper`를 사용할 수 없는 것으로 보인다
* 위 내용과 동일한 issue가 등록되어 있으나 8개월째 처리되지 않음... [링크](https://github.com/kirill-konshin/next-redux-wrapper/issues/455)

# 작성중...
