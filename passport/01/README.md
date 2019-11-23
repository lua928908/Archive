# passport - 로그인 관리

### passport란

passport는 npm 모듈이며 로그인처리 및 session 관리를 편리하게 도와준다.
passport는 독특하게 Strategy(전략)이라는 개념을 사용한다  passport-local은 로그인을 직접 구현할 때 사용하고 passport-google-oauth, passport-facebook, passport-twitter, passport-kakao, passport-naver 등등 oauth를 통해 SNS가입을 관리하는것도 지원한다.



## localStrategy 구현하기


```
npm i passport
```
passport 모듈을 다운로드 받는다.

```
App.js - Root 파일

    const passport = require('passport');
    const passportConfig = require('./passport/index');
    passportConfig(passport);

    // express.session이 먼저 선언되어 있어야 한다.
    app.use(passport.initialize()); // passport 설정 초기화
    app.use(passport.session()); // 사용자 정보를 session에 추가한다, passport.deserializeUser() 실행
```
위 코드처럼 app.js에서 passport를 app.js에 붙인다. 

```
localStrategy(일반로그인) - router 설정


    router.post('/login', (req, res, next)=>{
        passport.authenticate('local', (authError, user, info)=>{
            if( authError ){
                // 에러가 발생한 경우
                console.error(authError);
                return next(authError);
            }
            if( !user ){
                // 에러도 아니고 사용자 정보도 없는경우
                req.flash('loginError', info.message);
                return res.redirect('/');
            }
            return req.login(user, (loginError)=>{ // passport에서 req.login을 사용가능하게 해준다.
                if( loginError ){
                    console.error(loginError);
                    return next(loginError);
                }
                // 로그인에 최종적으로 성공했다면 req.user를 통해 로그인된 사용자정보를 가져올 수 있다.
                return res.redirect('/');
            });
        })(req, res, next)
    });

    /*
        localStrategy.js 에서 작성했던 done(에러, 성공, 실패) 코드가
        아래의 코드로 전달된다.
    
        passport.authenticate('local', (authError, user, info)=>{
            // code
        })(req, res, next)
    */
```
위에 코드는 로그인 요청이 들어왔을때의 router에서 어떻게 동작할건지에 대한 코드이다.

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
위에 코드는 passport 전략들을 index.js로 모아서 관리하기위한 내용이다.

```
passport/localStrategy.js - local에서 로그인할때 사용할 전략(Strategy)



    const LocalStrategy = require('passport-local').Strategy;
    const bcrypt = require('bcrypt');
    const { User } = require('../models/index');

    module.exports = (passport)=>{
        passport.use(new LocalStrategy({
            // usernameField와 passwordField는 패키지에서 정해진 프로퍼티 이다.
            usernameField: 'id',
            passwordField: 'password',
        }, async (id, password, done)=>{ // done(에러, 성공, 실패);
            try{
                const exUser = await User.findOne({ where: { user_id: id } });
                if( exUser ){
                    // bcrypt.compare는 비교를 해주는 패키지의 기본 메서드 이다.
                    const result = await bcrypt.compare(password, exUser.password);
                    if( result ){
                        done(null, exUser);
                    }else{
                        done(null, false, { message: '비밀번호가 일치하지 않습니다.' });
                    }
                }else{
                    done(null, false, { message: '가입되지 않은 회원입니다.' });
                }
            }catch(error){
                console.error(error);
                done(error);
            }
        }));
    };


    /*
        done(서버에러) -> try,catch에서 catch문에 잡혔다면 에러가 발생한 것이니 done()함수안에 error를 넣어준다.
        done(null, 사용자정보) -> 로그인에 성공한 경우
        done(null, false, 실패정보) -> 로그인에 실패했으니 사용자 정보는 false고 실패정보 메세지를 담는다.
    */
```


유의해야할 내용은 처음 사용자가 아이디,비밀번호를 입력해 passport가 실행될때는 passport.serializeUser 가 실행되고
로그인이 되어있는 상태에서는 passport.deserializeUser가 실행된다는 점이다.

현재 회사에서는 JWT를 통한 로그인, session관리를 하고있지만 이전에 내가혼자 만들때에는 passport를 통해 쿠키로 관리를 했었다
백엔드를 잘모르는 사람도 passport를 통해 편리하게 로그인관리를 할 수 있을것이라 생각한다.
