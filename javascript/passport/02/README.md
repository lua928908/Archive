# passport - 로그인 관리

## 카카오로그인 구현하기

이어서 kakaoStrategy를 구현해보자, 카카오가 비교적 사용하기 쉽다.

```
npm i passport-kakao
```
passport-kakao를 다운로드 한다.

```
route - "/kakao"라는 주소로 get요청을 받을때 


    // (1)
    router.get('/kakao', passport.authenticate('kakao'));

    // (3)
    router.get('/kakao/callback', passport.authenticate('kakao', {
        failureRedirect: '/',
    }), (req, res) => {
        res.redirect('/');
    });
```


```
passport/index.js - passport Root파일

    const local = require('./localStarategy');
    const kakao = require('./kakaoStrategy');
    const { User } = require('../models/index');

    module.exports = (passport)=>{
        // 최초 로그인할때
        passport.serializeUser((user, done)=>{
            done(null, user.id);
        });

        // 로그인
        passport.deserializeUser((id, done)=>{
            User.findOne({ where: { id } })
                .then((user)=>{
                    done(null, user);
                })
                .catch((error)=>{
                    console.error(error);
                    next(error);
                });
        });

        local(passport);
        kakao(passport);
    }
```

1편에서 작성한것 처럼 passport를 관리하기위한 Root 팡리이다.


```
passport/KakaoStrategy.js - SNS로그인 시 사용할 전략(Strategy)


    const KakaoStrategy = require('passport-kakao').Strategy;
    const { User } = require('../models/index');

    // (2) (4)
    module.exports = (passport)=>{
        passport.use(new KakaoStrategy({
            clientID: process.env.KAKAO_ID, // 카카오앱 ID
            callbackURL: '/kakao/callback', // 로그인 성공후 객체를 받을 주소
        }, async (accessToken, refreshToken, profile, done)=>{
            try{
                const exUser = await User.findOne({
                    where: {
                        snsId: profile.id,
                    },
                });
                if( exUser ){
                    done(null, exUser);
                }else{
                    // 이메일 -> profile._json && profile._json.kaccount_email
                    const newUser = await User.create({
                        user_id: profile.id,
                        nick: profile.displayName,
                        snsId: profile.id, // db에 snsId라는 항목이 있어야함
              provider: 'kakao', // db에 provider라는 항목이 있어야함
                    });
                    done(null, newUser);
                }
            }catch(error){
                console.error(error);
                done(error);
            }
        }));
    }

```
코드에 써있는 (1),(2) 이런주석들은 실행수를 주석으로 달아놓은 것이다.

### 추가기능 - middleWare로 로그인,비로그인 체크하기
```
exports.isLoggedIn = (req, res, next) => {
        if (req.isAuthenticated()) {
            next();
        } else {
            res.status(403).send('로그인 필요');
        }
    };
    exports.isNotLoggedIn = (req, res, next) => {
        if (!req.isAuthenticated()) {
            next();
        } else {
            res.redirect('/');
        }
    };
    /*
        passport에서 로그인에 성공하면
        req.user, req.login, req.logout 와 같은 기능을 사용할 수 있게된다
        req.isAuthenticated 는 로그인 여부를 알 수 있다.
    */
```

위 코드처럼 isLOggedIn, inNotLoggedIn 이라는 2개의 함수를 만들어
라우터에 접근할때 사용자가 로그인상태인지 아닌지를 관리할 수 있다.

예를들어
```
글쓰기 라우터

    router.post('/', isLoggedIn, async (req, res, next) => {
    	try{
    		// 글쓰기를 처리할 코드
    	}catch(e){
    		console.error(e);
    		next(e);
    	}
    });
```

위에 코드처럼 router.post에 두번째 인자로 isLoggedIn를 주면 로그인되어있는지를
먼저 확인한 이후 콜백함수가 동작하니까 로그인/비로그인 여부를 관리할 수 있게된다.
