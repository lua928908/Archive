# redux-saga 배우기

## redux란 무엇인가?

redux는 react와 가장 많이 사용되지만 redux자체로 존재할 수 있다, 리액트에서 가장많이 쓰이지만 노드나 vue에서도 사용가능하다.

#### redux 와 react 연결하기 - app.js

```
import React from 'react';
import Head from 'next/head';
import withRedux from 'next-redux-wrapper';
import withReduxSaga from 'next-redux-saga';
import { applyMiddleware, compose, createStore } from 'redux';
import { Provider } from 'react-redux';
import createSagaMiddleware from 'redux-saga';

import AppLayout from '../components/AppLayout';
import reducer from '../reducers/index';
import rootSaga from '../sagas';

const luaBlog = ({ Component, store }) => {
    return (
        <>
            <Provider store={store}>
                <Head>
                    <title>Lua - Blog</title>
                    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/react-draft-wysiwyg@1.13.2/dist/react-draft-wysiwyg.css" />
                    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/antd/3.16.2/antd.css" />
                </Head>

                <AppLayout>
                    <Component children={Component} />
                </AppLayout>
            </Provider>
        </>
    );
};

const configureStore = (initialState, options) => { // 02. next-redux-wrapper모듈이 root컴포넌트가 store를 prop로 받을 수 있게 해준다.
    const sagaMiddleware = createSagaMiddleware();
    const middlewares = [sagaMiddleware];
    const enhancer = process.env.NODE_ENV === 'production'
    ? compose(applyMiddleware(...middlewares))
    : compose(
        applyMiddleware(...middlewares),
        !options.isServer && typeof window.__REDUX_DEVTOOLS_EXTENSION__ !== 'undefined' ? window.__REDUX_DEVTOOLS_EXTENSION__() : f => f,
    );
    const store = createStore(reducer, initialState, enhancer); // 01. redux모듈이 사용자가 만든 reducer를 토대로 store를 생성
    store.sagaTask = sagaMiddleware.run(rootSaga);
    return store;
};


export default withRedux(configureStore)(withReduxSaga(luaBlog));
```

내가 블로그를 직접 만들려고 했었을 때 사용했던 코드의 일부이다. 일반 react를 사용할때 redux의 구현과 next를 사용하는 react의 redux 구현은 조금다르다.
`npm i next-redux-wrapper` 를 설치해야한다. app.js ( react root 컴포넌트)에서 redux를 사용하기위해 store를 props 형태로 받아야하는데 그일을 해주는것이 next-redux-wrapper이다.


## redux-saga란 무엇인가?

redux는 어플리케이션 전체에서 데이터 혹은 캐시를 다루는 과정에서 비동기의 동작을 손쉽게 하기위한 라이브러리 이다.
결국 순수하지 않은 비동기 동작들을, 더 쉽고 좋게 만들기 위함이다. 그리고 쉽고 좋게 만들기위한 방법으로 제너레이터를 사용한다.
 
 
## 제너레이터(Generator)란 무엇인가?
 
 제너레이터를 이해해야 하는 이유는 redux-saga가 비동기를 효과적으로 구현하기 위해 제너레이터를 사용하기 때문이다.
 
 ES6에서 도입된 제너레이터(Generator) 함수는 이터러블을 생성하는 함수이다.
 제너레이터 함수를 사용하면 이터레이션 프로토콜을 준수해 이터러블을 생성하는 방식보다 간편하게 이터러블을 구현할 수 있다. 또한 제너레이터 함수는 비동기 처리에 유용하게 사용된다.
 쉽게 표현하자면 제너레이터는 함수의 동작을 멈췄다가 다시 동작하게 했다가 하는등의 동작을 가능하게 한다.
 
 
```
function* generatorExam(){
	yield 1;
	yield 2;
	yield 3;
	yield 4;
	return "끝";
}

const example = generatorExam();

console.log( example.next() ) // 1
console.log( example.next() ) // 2
console.log( example.next() ) // 3
console.log( example.next() ) // 4
console.log( example.next() ) // 5
console.log( example.next() ) // 끝
```

제너레이터는 `function*` 로 선언한다. 펑션뒤에 별이 붙는다. 오타가 아니다
제너레이터함수를 실행한 결과를 변수에 담고 .next를 하면 yield 뒤에 값을 반환한다.
다시 .next를 하면 그다음번 yeild 뒤 값을 반환한다. 

![제너레이터 예제 실행결과](../images/캡처.PNG)

위에 실행결과를 보자 `example.next()`를 하고나면 `{value: 1, done: false}` 라는 결과가 나오게 된다. 이렇게 제너레이터는 yield를 통해 함수의 실행을 멈추는 것이 가능하게 해준다.

<br>

## react 컴포넌트에서 redux 사용하기 - pages/index.js 

```
import React, { useEffect } from 'react';
import PostForm from '../components/PostForm';
import PostCard from '../components/PostCard';
import { useDispatch, useSelector } from 'react-redux';
import { LOG_IN, LOG_OUT } from '../reducers/user';

const dummy = {
    isLoggedIn: true,
    imagePaths: [],
    mainPosts: [{
        User: {
            id: 1,
            nickname: 'LUA',
        },
        content: '첫 번째 게시글',
        img: 'https://t1.daumcdn.net/cfile/tistory/274FCA495215649B02'
    }],
};

const Home = () => {
    const dispatch = useDispatch(); // react-redux 모듈에서 useDispatch를 통해 dispatch를 사용할 수 있다.
    const { isLoggedIn, user } = useSelector(state => state.user); // react-redux 모듈에서 useSelector 통해 state값을 가져올 수 있다.

    useEffect(() => {
        dispatch({
            type: LOG_IN,
            data: {
                nickname: '루아',
            }
        })
    }, [])
    
    return (
        <>
            {user ? <div>로그인 했습니다: {user.nickname}</div> : <div>로그아웃 했습니다.</div>}
            {dummy.isLoggedIn && <PostForm />}
            {dummy.mainPosts.map((c) => {
                return (
                    <PostCard key={c} post={c} />
                );
            })}
        </>
    );
};

export default Home;
```

redux를 사용해 action을 dispatch하게되면 무조건 동기로 작동하게된다, 예를들어 로그인에 대한 action을 한다면 로그인action을 dispath하는순간 바로 state가
변경되어 버린다. 실제로는 로그인을 위한 서버에 요청을 보내고 응답을 받은뒤 state의 변경이 이루어져야 하는데 모두 동기로만 작동하기 때문에 action을 비동기적으로
사용하기 위해 redux-saga 를 이용해 redux를 통한 action을 할때 미들웨어를 이용해 비동기로 실행하도록 구현한다. 그과정에서 generator 를 사용한다.


## rootSaga 설정하기 - front/sagas/index.js
 
```
import {all, call} from 'redux-saga/effects';
import user from './user';
import post from './post';

export default function* rootSaga() {
    yield all([
        call(user),
        call(post),
    ]);
};
```

## login을 위한 saga 코드 - front/sagas/user.js

```
import { all, fork, takeLatest, takeEvery, call, put, delay } from 'redux-saga/effects';
import {
	LOG_IN_REQUEST, LOG_IN_SUCCESS, LOG_IN_FAILURE,
	SIGN_UP_REQUEST, SIGN_UP_SUCCESS, SIGN_UP_FAILURE,
	LOG_OUT_REQUEST, LOG_OUT_SUCCESS, LOG_OUT_FAILURE,
	LOAD_USER_REQUEST, LOAD_USER_SUCCESS, LOAD_USER_FAILURE,
	FOLLOW_USER_REQUEST, FOLLOW_USER_SUCCESS, FOLLOW_USER_FAILURE,
	UNFOLLOW_USER_REQUEST, UNFOLLOW_USER_SUCCESS, UNFOLLOW_USER_FAILURE,
	LOAD_FOLLOWERS_REQUEST, LOAD_FOLLOWERS_SUCCESS, LOAD_FOLLOWERS_FAILURE,
	LOAD_FOLLOWINGS_REQUEST, LOAD_FOLLOWINGS_SUCCESS, LOAD_FOLLOWINGS_FAILURE,
	REMOVE_FOLLOWER_REQUEST, REMOVE_FOLLOWER_SUCCESS, REMOVE_FOLLOWER_FAILURE,
	EDIT_NICKNAME_REQUEST, EDIT_NICKNAME_SUCCESS, EDIT_NICKNAME_FAILURE,
} from '../reducers/user';
import axios from 'axios';

// 로그인
function logInAPI(loginData){
	return axios.post('/user/login', loginData, {
		// 쿠키를 주고받는 옵션, 서버에서도 옵션을 설정해야가능.
		withCredentials: true
	});
};
function* logIn(action){
	try{
		const result = yield call(logInAPI, action.data);
		yield put({
			type: LOG_IN_SUCCESS,
			data: result.data,
		});
	}catch(e){
		console.error(e);
		yield put({
			type: LOG_IN_FAILURE,
			reason: e.response && e.response.data,
		});
	}
};
function* watchLogIn(){
	yield takeLatest(LOG_IN_REQUEST, logIn);
}

 // 회원가입
function signUpAPI(singupData){
	return axios.post('/user/', singupData);
};
function* signUp(action){
	try{
		yield call(signUpAPI, action.data);
		alert('회원가입 완료');
		yield put({
			type: SIGN_UP_SUCCESS,
		});
	} catch(e){
		console.error(e);
		yield put({
			type: SIGN_UP_FAILURE,
			error: e
		});
	}
};
function* watchSingUp(){
	yield takeEvery(SIGN_UP_REQUEST, signUp)
};

// 로그아웃
function logOutAPI(){
	// post요청은 data를 의미하는데 비어있는 경우 빈객체라도 넣어야함
	return axios.post('/user/logout/', {}, {
		withCredentials: true,
	});
};
function* logOut(){
	try{
		yield call(logOutAPI);
		yield put({
			type: LOG_OUT_SUCCESS,
		});
	} catch(e){
		console.error(e);
		yield put({
			type: LOG_OUT_FAILURE,
			error: e
		});
	}
};
function* watchLogOut(){
	yield takeLatest(LOG_OUT_REQUEST, logOut)
};

// 로드유저
function loadUserAPI(userId){
	// get요청은 data가 없어 두번째 인자가 옵션이 된다.
	return axios.get(userId ? `/user/${userId}` : '/user/', {
		withCredentials: true,
	});
};
function* loadUser(action) {
	try {
		const result = yield call(loadUserAPI, action.data);
		yield put({
			type: LOAD_USER_SUCCESS,
			data: result.data,
			me: !action.data,
		});
	} catch (e) {
		console.error(e);
		yield put({
			type: LOAD_USER_FAILURE,
			error: e,
		});
	}
};
function* watchLoadUser(){
	yield takeEvery(LOAD_USER_REQUEST, loadUser)
};

// 팔로우
function followAPI(userId) {
	return axios.post(`/user/${userId}/follow`, {}, {
		withCredentials: true
	});
  }
function* follow(action) {
	try {
		const result = yield call(followAPI, action.data);
		yield put({
			type: FOLLOW_USER_SUCCESS,
			data: result.data,
		});
	} catch (e) {
		console.error(e);
		yield put({
			type: FOLLOW_USER_FAILURE,
			error: e,
		});
		alert(e.response && e.response.data);
	}
};
function* watchFollow(){
	yield takeLatest(FOLLOW_USER_REQUEST, follow);
};

// 언팔로우
function unfollowAPI(userId) {
	return axios.delete(`/user/${userId}/follow`, {
		withCredentials: true
	});
  }
function* unfollow(action) {
	try {
		const result = yield call(unfollowAPI, action.data);
		yield put({
			type: UNFOLLOW_USER_SUCCESS,
			data: result.data,
		});
	} catch (e) {
		console.error(e);
		yield put({
			type: UNFOLLOW_USER_FAILURE,
			error: e,
		});
		alert(e.response && e.response.data);
	}
};
function* watchUnfollow(){
	yield takeLatest(UNFOLLOW_USER_REQUEST, unfollow);
};

// 나를 팔로워한 목록
function loadFollowersAPI(userId, offset = 0, limit = 3) {
	return axios.get(`/user/${userId || 0}/followers?offset=${offset}&limit=${limit}`, {
		withCredentials: true
	});
  }
function* loadFollowers(action) {
	try {
		const result = yield call(loadFollowersAPI, action.data, action.offset);
		yield put({
			type: LOAD_FOLLOWERS_SUCCESS,
			data: result.data,
		});
	} catch (e) {
		console.error(e);
		yield put({
			type: LOAD_FOLLOWERS_FAILURE,
			error: e,
		});
	}
};
function* watchLoadFollower(){
	yield takeLatest(LOAD_FOLLOWERS_REQUEST, loadFollowers);
};

// 내가 팔로잉한 목록
function loadFollowingsAPI(userId, offset = 0, limit = 3) {
	return axios.get(`/user/${userId || 0}/followings?offset=${offset}&limit=${limit}`, {
		withCredentials: true
	});
  }
function* loadFollowings(action) {
	try {
		const result = yield call(loadFollowingsAPI, action.data, action.offset);
		yield put({
			type: LOAD_FOLLOWINGS_SUCCESS,
			data: result.data,
		});
	} catch (e) {
		console.error(e);
		yield put({
			type: LOAD_FOLLOWINGS_FAILURE,
			error: e,
		});
	}
};
function* watchLoadFollowings(){
	yield takeLatest(LOAD_FOLLOWINGS_REQUEST, loadFollowings);
};

// 팔로잉 삭제
function removeFollowerAPI(userId) {
	return axios.delete(`/user/${userId}/follower`, {
		withCredentials: true
	});
  }
function* removeFollower(action) {
	try {
		const result = yield call(removeFollowerAPI, action.data);
		yield put({
			type: REMOVE_FOLLOWER_SUCCESS,
			data: result.data,
		});
	} catch (e) {
		console.error(e);
		yield put({
			type: REMOVE_FOLLOWER_FAILURE,
			error: e,
		});
		alert(e.response && e.response.data);
	}
};
function* watchRemoveFollower(){
	yield takeLatest(REMOVE_FOLLOWER_REQUEST, removeFollower);
};

// 닉네임 수정
function editNicknameAPI(nickname) {
	return axios.patch(`/user/nickname`, { nickname }, {
		withCredentials: true
	});
  }
function* editNickname(action) {
	try {
		const result = yield call(editNicknameAPI, action.data);
		yield put({
			type: EDIT_NICKNAME_SUCCESS,
			data: result.data,
		});
	} catch (e) {
		console.error(e);
		yield put({
			type: EDIT_NICKNAME_FAILURE,
			error: e,
		});
		alert(e.response && e.response.data);
	}
};
function* watchEditNickname(){
	yield takeLatest(EDIT_NICKNAME_REQUEST, editNickname);
};

export default function* userSaga (){
	yield all([
		fork(watchLogIn),
		fork(watchLogOut),
		fork(watchSingUp),
		fork(watchLoadUser),
		fork(watchFollow),
		fork(watchUnfollow),
		fork(watchLoadFollower),
		fork(watchLoadFollowings),
		fork(watchRemoveFollower),
		fork(watchEditNickname),
	]);
};

/*
	all: 
	call: 함수를 동기로 호출한다.
	fork: 함수를 비동기로 호출한다.
	take: 인자로 들어온 Actions이 실행되면 next()를 통해 함수를 재개한다. 실행후 함수재호출 불가능 ( done: true )
	takeEvery: 대부분의 Action은 여러번 실행이 가능해야 하기때문에 done: false가 유지되도록 해준다, 반복실행가능
	takeLatest: Action이 동시적으로 여러번 실행될 경우 최근실행된 Action만 유효하게 취급한다.
	delay: 타이머기능, ms 단위로 딜레이를 만든다.
	race:
	cancel:
	select:
	throttle: 같은 함수가 계속 호출되는것은 막아준다(request 요청 중복을 방지함), scroll 이벤트에 많이사용
*/
```

예를들어 버튼을 클릭함으로 인해 `LOG_IN` 타입을 dispatch 하게되면 `watchLogIn`이라는 watch함수가 `LOG_IN`이라는 dispatch가 발생하는지 아닌지 계속 감시하다가
logIn함수를 실행시키고 logIn 함수가 logInAPI를 실행시킨다. 그럼 logInAPI는 궁극적으로 axios를 통해 restAPI(예제에서의 백엔드 서버는 node로 만듬 https://github.com/lua928908/react-sns-publish/tree/master/back 참고)로 요청을 보낸다.
axios 요청이 성공하게되면 다음줄인 LOG_IN_SUCCESS가 실행되고 reducers에 있는 LOG_IN_SUCCESS가 switch문에 걸리며 redux에 값이 삽입되게된다. 그래서 redux-saga로 비동기 함수를 만들때
watch 함수와 axios요청함수 그리고 요청이후 success인지 fail인지에 따라 동작을 해줄 3가지의 함수가 셋트처럼 만들어져야 한다. 다소 번거로울수 있어도 나중에는 오류를 해결하거나 상황을 파악하는게 많은 도움이 된다.

__버튼클릭  ->  dispatch({type: LOG_IN})  ->  watchLogIn  ->  logIn  ->  logInAPI  ->  success or fail (another dispatch)  ->  reducer <br>__

이런 형태로 기억해주면 될것 같다.

전체 코드는 [여기](https://github.com/lua928908/react-sns-publish)에서 천천히 살펴보길 바란다.
