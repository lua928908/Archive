# HTTP 공부하기 01

참고할 만한 자료 <br>

[HTTP 완벽 가이드](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=49731592) <br>
[김영한 - 모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)


<br>
<br>
<br>


### 왜 HTTP를 공부해야 하는가

- 요즘 웹에서는 모든것이 HTTP 프로토콜 위에서 동작한다. 앱과 서버가 통신하거나 서버와 서버가 통신하거나 이미지나 HTML, 영상, 기타파일 등등 모두 HTTP로 주고 받기 때문에 백엔드와 프론트개발자 모두 HTTP에 대한 이해가 필요하다.
- HTTP를 제대로 이해하지 못한 상태에서 그냥 개발 경험을 쌓다보면 여러 오류를 만나게 될 것이다, HTTP를 이해하는것이 나중에 다른 개발 경험을 하는데에도 큰 도움이 된다.
- 특히 백엔드 개발자라면 HTTP에 대한 이해가 있어야 REST API를 설계하는데 중요한 지식이 된다. 웹 개발자라면 계속해서 HTTP 위에서 개발을 해야한다.  집 주변 도서관에서  HTTP 완벽 가이드 를 읽어보길 추천한다, URL 개념부터 차근차근 알려준다.


## 네트워크에 대해 알아보자

- HTTP 프로토콜을 이해하기 위해서는 기본적으로 네트워크 기본적인 이해가 있어야 한다.
- 멀리 살고있는 누군가에게 내 파일을 전송하고 싶다면 인터넷을 통해 파일을 전송해야 한다. 그런데 인터넷망이 고속도로 처럼 일자로 쭉 뻗어 있는 형태가 아니다, 여러 복잡한 과정을 거치며 파일이 전달된다, 한국에서 미국까지 파일을 전송한다면 중간에 수많은 노드를 거쳐 데이터가 전송되어야 하는데 그 과정을 알아보자


<br>

### IP 프로토콜

인터넷에 연결된 클라이언트 들은 IP주소를 가지게 된다, 할당받은 IP Address를 통해 데이터를 주고받을 수 있고, 패킷(Packet) 이라는 통신 단위로 데이터를 주고 받는다. 패킷을 주고받을 때에는 출발지와 목적지에 대한 정보, 기타정보를 가지고 IP패킷을 만들어서 인터넷망을 통해 노드들이 서버 패킷정보를 전달하며 최종 목적지로 전달되게 된다. 
출발지 IP패킷 생성 (출발지IP주소, 목적지IP주소, 기타정보) -> 인터넷 망 -> 목적지 도착 (다시 응답) -> 인터넷 망 -> 출발지로 Response 
위 예시에서 처음 출발지에서 IP패킷을 생성하고 목적지에 도착할 때의 루트와 응답이 출발지로 전달될 때의 루트는 다를수 있다, 워낙 네트워크망이 복잡하기 때문이다.


### IP프로토콜의 한계

- 비연결성 - 패킷을 받을 대상(도착지)이 없거나 서비스 불능(오프라인) 상태여도 출발지에서는 알 수 없고 패킷은 그대로 전송 된다.
- 비신뢰성 - 인터넷은 결국 서버들끼리의 통신을 통해 데이터를 주고 받는데 만약 중간에 서버가 손상된다면 패킷이 유실되거나 큰 용량의 패킷을 보내야 하는경우 여러개의 패킷으로 쪼개서 생성해 보내는데 패킷이 순서대로 처리되지 않을 수도 있다. (한 패킷에 약 1500바이트) HELLO, WORLD! 라는 정보를 보낼때 크기가 크면 2번으로 나누어 보내는데 1번 패킷을 보낸이후 2번패킷을 보낼때 다른루트가 더 빠르다고 판단해 다른 루트로 패킷을 보낼수도 있다.  패킷의 순서가 1,2번이 아닌 2,1번으로 도착할 수도 있다.
- 프로세스 구분 - 같은 IP를 사용하는 서버에서 여러개의 애플리케이션이 동작한다면 문제가 된다. (한대의 PC에서 게임도 하고 음악도 듣고 채팅도 한다면 출처를 어떻게 구분할 것인가)

<br>

### TCP / UDP (IP프토콜의 한계를 극복하자)

```
인터넷 프로토콜 스택의 4계층 (TCP를 통해 위에 언급 되었던 문제들을 극복할 수 있다.)


* 애플리케이션 계층 -HTTP, FTP
* 전송 계층 - TCP, UDP
* 인터넷 계층 - IP
* 네트워크 인터페이스 계층 (랜카드, 랜드라이버)
```


#### 프로토콜 계층 순서
1. 웹 브라우저에서 Hello, World 라는 메세지 생성 (http가 생성 됨)
2. Socket 라이브러리를 통해 전달 (TCP로 감싼다.)
3. IP 패킷이 얹어진다. (IP 패킷 생성, TCP 데이터 포함)
4. 랜카드를 통해 인터넷 서버로 전송.


위에 순서는 비유가 포함되어 있기 때문에 `감싼다` 등의 표현은 감안해야 한다. 
이렇게 여러계층으로 전송하고자 하는 데이터가 생성된다.
IP안에는 출발지 IP와 목적지 IP 기타정보가 포함된다 했다, 그리고 TCP에는 출발지 PORT, 목적지 PORT, 전송 제어, 순서, 검증정보 와
같은 정보들이 포함되게 된다.


### TCP 특징 - 전송 제어 프로토콜 (Transmission Control Protocol)


* 연결지향 - TCP 3 way handshake 가상연결 (목적지의 네트워크가 유효한지 확인)
* 데이터 전달 보증
* 순서 보장
* 신뢰 할 수 있는 프토콜 이다.
* 대부분의 애플리케이션에서 TCP 사용



#### TCP 3 way handshake란?

1. 클라이언트가 서버에서 SYN(Synchronization) 라는 메세지를 보낸다, 데이터를 보내기전 동기화를 위함
2. 서버가 응답으로 ACK(Acknowledgement)라는 응답을 보낸다, 동시에 서버가 다시 클라이언트에게 SYN 메세지를 보낸다.
3. 양쪽이 모두 ACK(Acknowledgement) 라는 상태가 된 이후 데이터를 보낸다
4. 이것은 가상연결 이기 때문에 실제 연결이 된 것은 아니다, 클라이언트와 서버가 모두 SYN/ACK 를 주고 받았기 때문에 논리적으로 연결 되었다고 판단하는 것이고 실제 데이터를 전송하는 과정에 중간에 수 많은 서버가 있을 것인데 이 중간서버인 노드들은 클라이언트와 서버가 연결되었는지 모른다. (나를 위한 전용 랜선이 보장 되는게 아니다)

<br>

#### 순서보장
1. 클라이언트가 데이터가 크다면 패킷1, 패킷2, 패킷3 등으로 나누어 보게 된다.
2. 만약 서버가 패킷을 받을때 순서가 다르다면 데이터를 버리고 다시 보내라고 한다. (서버에 따라 최적화가 다를 수 있음, 패킷을 모았다가 정렬 하는 등)
3. TCP/IP 패킷에 TCP계층에서 전송제어 와 순서에 대한 정보들이 들어있기 때문에 가능하다

<br>

### UDP는 뭐지? (User Datagram Protocol)


* UDP는 하얀 도화지에 비유된다, 기능이 거의 없다.
* 연결지향 - TCP 3 way handshke 같은 기능 X
* 데이터 전달 보증 X
* 순서 보장 X
* 단순하고 빠르다.
* IP와 거의 같고 PORT+체크섬 정도 기능만 가이고 있다. (하나의 IP에서 PORT를 통해 여러개로 구분하게 한다. )
* 애플리케이션에서 추가 작업이 필요하다. (TCP는 느린데 이걸 더 최적화 하고 싶다면 UDP를 가지고 본인이 직접 튜닝해야 한다.)
* 요즘 UDP가 오히려 각광이라고 한다. HTTP3가 핸드쉐이킹을 줄이고자 UDP를 채택 하였다고 한다.  [HTTP3가 UDP를 채택한 이유](https://evan-moon.github.io/2019/10/08/what-is-http3/)

<br>

### PORT

![PORT 이미지](../images/Screenshot%202021-07-17%20at%2013.35.07.jpg)

<br>

포트는 항구라는 뜻이다 하나의 클라이언트 PC에서 여러대의 서버에 연결되어야 한다면,
예를들어 웹브라우저 에서 쇼핑을 하면서 게임도 켜놓고 음악도 듣고 있다면 나의 IP로 온 패킷이
게임에 대한 패킷인지 웹 브라우저의 응답 결과로 온 패킷인지 알 수 없게된다. IP만으로는 이 문제가
해결되지 않고 그래서 TCP/IP 패킷을 주고 받을 때 출발지의 PORT와 도착지의 PORT 정보도 포함되어 있다.

`PORT는 같은 IP 내에서 프로세스를 구분하는데 목적이 있다.`

* 0 ~ 65535 까지 port를 할당할 수 있다.
* 0 ~ 1023 이미 사용되는 알려진 포트, 사용하지 않는게 좋다.
* FTP - 20, 21
* TELNET - 23
* HTTP - 80
* HTTPS - 443

<br>
<br>


## URI 와 웹 브라우저 요청 흐름

URI는 Uniform Resource Identifier, 리소스를 식별하는 통합된 방법 정도의 의미로 이해할 수 있을 것 같다.

* URI
* URL
* URN

![uri 개념](../images/Screenshot%202021-07-18%20at%2014.39.53.jpg)

세가지는 다른 개념이다. URN은 거의 처음들어 보았는데 [여기](https://www.ietf.org/rfc/rfc3986.txt)에서 `1.1.3.  URI, URL, and URN` 이라는 목차를 보면 세 정의를 확인해 볼 수 있다.
URI 라는 리소스를 식별하는 가장 큰 개념이 있고 그 안에 URL (Locator)과 URN(Name) 이 포함된다.
URL은 그냥 해당 위치에 리소스가 있다 라는 의미이고 URN은 해당 리소스의 이름이다.

![url과 uln 형태](../images/Screenshot%202021-07-18%20at%2014.40.38.jpg)

URN은 이런 형태의 모습 이다, 대부분 URL을 사용하고 url은 위치에 대한 정보여서 바뀔수도 있다 urn은 이름이기 때문에 바뀌지 않는다.
url보다 더 좋을 것 같지만 urn은 책의 ISPN 정보를 구분하는 형태에서 사용되기도 한다고 하나 경험할 일이 잘 없을 듯 하다.

<br>

### URL schema

* schema: //[userInfo@]host[:port][/path][?query][#fragment]
* https://www.google.com:443/search?q=hello&hl=ko
* 맨위에 언급했던 HTTP 완벽가이드 책에 URI 관련내용이 구체적으로 잘 설명되어 있다.

<br>

#### 웹 브라우저 요청 흐름

* https://www.google.com/:443/search?q=hello&hl=ko

위 요청이 브라우저로 전달된다면 www.google.com 라고하는 정보를 DNS에서 조회해 IP를 알아내고

```
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com
```

이런 모습으로 HTTP 요청 메세지가 생성될 것이다, 그리고 IP정보와 포트정보를 알고 있으니 SOCKET 라이브러리를 통해 
TCP/IP로 `www.google.com` 호스트에 SYN/ACK 를 통해 연결되었는지 확인할 것이다 (가상연결), HTTP 메세지에 TCP/IP 패킷정보를 담아
네트워크를 통해 서버로 요청이 전달되고 수 많은 인터넷 노드를 통해서 구글로 요청이 전달될 것이다. 요청을 받는 서버는 도착한 HTTP 요청을 파싱해서 HTTP 요청 메세지를 생성하고
TCP/IP 패킷을 만들어서 응답을 해주고 클라이언트로 전달된 응답 HTTP 메세지를 브라우저가 화면에 보여주게 된다.

```
참고로 계속 언급되는 노드란 Node.js를 말하는게 아니다 컴퓨터 과학에 쓰이는 기초적 단위를 의미하고 HTML에서 가장 작은 단위에 요소를 node 라고 표현 한다.
마찬가지로 여기서 의미하는 노드는 Node.js가 아니라 컴퓨터과학이나 통신망에서 의미하는 노드이다. 
```

<br>
<br>

## HTTP 기본

### 모든것이 HTTP 이다.

http는 HyperText Transfer Protocol 이다, http는 기본적으로 html 문서간에 링크를 연결할 수 있는 파일을 전송하기 위한 프로토콜 이였다.
그런데 지금은 음성, 영상, 이미지, JSON, XML, text 등등 서버와 서버간에 데이터를 주고받을 때에도 HTTP르 사용한다.
서버간에 TCP프로토콜로 직접 연결해 데이터를 전송하는 경우는 잘 없다고 한다. 최근 모바일 게임에서는 http로 통신을 주고받는 구조로도 많이 개발한다는데
잘 모르겠다.

<br>

### HTTP의 역사
이미 중요한 핵심 내용들은 HTTP/1.1 버전에서 대부분다 개발 되었다.

* HTTP/0.9 1991년 개발, GET 메서드만 지원, HTTP 헤더가 없다.
* HTTP/1.0 1996년 개발, 메서드 와 헤더가 추가
* HTTP/1.1 1997년 개발, 대부분의 웹이나 어플리케이션에서 사용되는 핵심적인 버전이다.
  * RFC2068, 1997년 개정 됨
  * RFC2616, 1999년 개정 됨 (이 버전으로 설명되는 책이 많음, 이런 스펙들은 발전이 느리고 보수 적이다.)
  * RFC7230~7235 2014년 개정됨
* HTTP/2 2015년 개발, 성능이 개선 됨
* HTTP/3 개발 진행중, TCP 대신 UDP를 채택해 성능개선을 하고 있다고 한다.

<br>

### StateFul, StateLess

http의 중요한 특징중에는 무상태 프로토콜을 지향한다는 것이다. 서버가 클라이언트의 상태를 보존하지 않는다.
즉, 1분에 서버에 어떤 요청을 보낸후 지금 다시 요청을 보낸다면 1분 전 요청을 보낸 클라이언트와 지금 요청 보내는 클라이언트가 같은 사람인지 모르는다는 것이다.

![stateful](../images/Screenshot%202021-07-20%20at%2020.17.48.jpg)


* 장점으로는 서버의 확장성이 높다. (무상태는 응답 서버를 쉽게 바꿀수 있다 -> 무한한 서버 증설 가능)
  * 만약 Stateful 상태라면 중계서버가 서버1에서 하던 작업을 서버2로 위임하기 어려워 진다. (이전 상태를 유지해줘야 하기 때문에)
  * 서버가 중간에 장애가 발생해도 다른 서버로 요청을 위임하면 그만이게 된다.
* 단점으로는 서버가 클라이언트의 상태를 모르니 클라이언트가 추가데이터를 보내주어야 한다. (cookie, accessToken)

<br>

#### Stateless 실무 한계

* 모든 것을 무상태로 설계 할 수 있는 경우도 있고 없는 경우도 있다.
* 상태를 유리해야 되는 경우(로그인) 이라면 쿠키/서버세션, JWT 토큰을 사용해야 한다. (꼭 필요한 경우에만 최소한에 상태 유지를 해야 된다.)
* 요청을 위한 데이터를 다 받아야 한다.

### 비 연결성

#### 장점
* http는 요청과 응답을 할 때만 연결되고 연결이 유지되지 않고 끊어진다.
* 서버는 연결을 유지하지 않기 때문에 최소한의 자원으로 서버를 유지할 수 있다.
* 일반적으로 초 단위 이하의 빠른 속도로 응답한다.
* 수천명이 서비스를 이용해도 실제 서버에서의 동시처리 요청은 수십개 이하로 매우 작다. (어플리케이션에서 연속해서 검색버튼을 누르지는 않는다.)
*

#### 단점
* TCP/IP 연결을 매번 새로 맺어야 한다, 다음 페이지로 넘어 가기만 해도 연결을 새로 맺어야 하고 3 hands Shake (SYN/ACK)을 다시 해야 된다.
* 웹 브라우저로 사이트를 요청하면 HTML뿐 아니라 js, css, image 같은 다른 리소스를 같이 다운로드 받게 된다.
* 현재는 HTTP 지속 연결(Persistent Connections)로 문제를 해결한다.
* HTTP/2, HTTP/3에서 더 많은 최적화가 진행 되었다.

<br>


## HTTP 메세지

![http 메세지 스펙](../images/Screenshot%202021-07-20%20at%2020.39.20.jpg)

위 이미지를 보자, HTTP 메세지에는 스타트라인, 헤더필드, 메세지바디 등이 있다.

헤더부분과 http응답 메세지 사이에는 CRLF 라는 공백이 반드시 들어가야 한다.


### HTTP 메서드
아래 설명에 리소스라고 표현했는데 최근에는 Representation 라고 표현한다고 한다, 근데 그냥 MDN Web Docs에도 리소스라고
표현되었길래 리소스라고 표현하겠다.

HTTP에는 여러 메서드가 존재한다. 리소스 중심의 리소스를 매칭해서 URI를 설계 해야한다.
리소스와 행위를 분리해야 한다, 가장 중요한 것은 리소스를 식별하는 것이다.
HTTP 메서드는 클라이언트가 서버로 요청을 할 때 기대하는 행동이다. 

* GET : 리소스 조회
* POST : 요청 데이터 처리 (주로 등록, 꼭 등록을 의미하지 않는다 요청한 데이터를 처리하는 것을 모두 포함한다.)
* PUT : 리소스 대체, 해당 리소스가 없으면 생성을 의미한다. (덮어씌우는 의미도 있다.)
* PATCH : 리소스의 부분 변경
* DELETE: 리소스 삭제
* HEAD : GET과 비슷하지만 메세지 부분을 제외하고, 상태 줄과 헤더만을 반환하다. (startline 과 header만 리턴한다.)
* OPTIONS : 대상 리소스에 대한 통신 가능 옵션을 설명한다. (CORS 체크에 주로 사용 됨)
* CONNECT : 요청한 리소스에 대해 양방향 연결을 시작하는 메소드, 터널을 열기위해 사용된다. [추가설명](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods/CONNECT)
* TRACE (en-US) : 대상 리소스의 경로를 따라 메시지 루프백 테스트를 수행하여 디버깅 메커니즘을 제공한다. [추가설명](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/TRACE)

REST API를 설계할 때는 리소스 기반으로 설계해야 하지만 실제로는 오롯이 리소스 위주로 URI를 설계하고 http 메서드만으로 행위를 구분하기는 어렵다.
그렇기 때문에 `POST /orders/{id}/start-delivery` 와 같은 설계가 필요하기도 한데 이런경우를 컨트롤URI 라고 부른다. (동사를 사용할 것)

<br>

### HTTP 메서드 속성

* 안전 (Safe Methods)
* 멱등 (Idempotent Methods)
* 캐시가능 (Cacheable Methods)

<br>


#### 안전 (Safe Methods)
호출해도 리소스를 변경하지 않는다, 예를들어 GET 메서드는 단순 조회이므로 Safe 하다고 볼 수 있다. 호출했을 때 변경이 발생하지 않는것을 Safe 하다고 한다.
GET 과 HEAD는 Safe 하다.

<br>


#### 멱등 (Idempotent Methods)
```
f(f(x)) = f(x)
```
멱등 참 애매한 표현이다, 한번 호출하든 두번 호출하는 결과는 늘 동일하다.
GET 메서드는 멱등 이라고 할 수 있다, PUT 메서드도 멱등 이라고 할 수 있다. 클라이언트가 똑같은 데이터를 PUT 한다면
최종 결과는 똑같다. `{name: 'a', age: 10}` 이라는 데이터를 10번을 PUT하면 최종 결과물은 동일하다 그래서 멱등이다.
DELETE 메서드도 멱등이다 마찬가지로 특정 리소스를 DELETE를 10번을 호출해도 최종 결과는 동일하기 때문에 멱등하다.
_그런데 POST는 멱등하지 않다, 결제를 2번하거나 게시물을 2번 만들면 최종 결과가 동일하지 않다._

<br>

그럼 왜 이런 멱등이라는 개념이 필요할까? 자동 복구 메커니즘과 같은 상황에서 필요하다, 예를들어 DELETE를 요청했는데 응답이 오지않았다면 다시한번
같은 DELETE 요청을 보내는 것이다. 멱등하니까 다시 요청해도 괜찮기 때문이다.
주의할 부분은 멱등은 외부 요인으로 중간에 리소스가 변경되는 것 까지 고려하지는 않는다. (사용자1은 계속 GET을 하는데 사용자2가 중간에 PUT을 하는경우 사용자1은 같은요청에도 결과가 달라졌지만 이런상황까지 고려하지 않는다.)

#### 캐시가능 (Cacheable Methods)

만약 웹브라우저에서 큰 이미지 리소스를 응답받는 경우 응답 결과 리소스를 캐시해서 사용할 수 있는가 하는 여부이다.
스펙상으로는 `GET, HEAD, POST, PATCH`에 캐시 가능하다고 되어있지만
실제로는 GET, HEAD 정도만 캐시로 사용한다. POST나 PATCH는 본문내용(바디의 내용)까지 캐시 key로 고려를 해야하는데 쉽지 않다.

<br>
<br>

### 클라이언트 -> 서버로 데이터 전송

클라이언트가 서버로 부터 데이터를 전송할 때에는 크게 두가지 방식이 있다, 쿼리파라미터 와 메세지 바디를 통한 전송 이다.

* 정적 데이터 조회: 그냥 GET `/static/example.jpg`와 같이 정적인 파일을 조회할 때는 아무 데이터없이 GET으로 리소스를 조회하기만 하면 된다.
* 동적 데이터 조회:
  * 쿼리 파라미터 : GET으로 동적 데이터를 조회해야 하는 경우, 주로 게시판 목록을 필터하거나 검색하는 경우 URI의 마지막 부분에 물음표와
    함께 key, value의 모양으로 데이터를 전송한다. `google.com/search?q=hello&hl=ko` 이 때 모든 값은 `String` 타입으로 전달된다.
  * HTML Form 전송 : html 문서중 form 태그가 존재한다. form 태그에 method와 action을 통해 어디로 전송할지 정할 수 있고, submit을 하면 form의
    데이터를 읽어서 HTTP 메세지로 만들고 form안에 입력했던 내용을 body에 담아 전송하는 것이다.
    이 때 Content-Type 은 `application/x-www-form-urlencoded` 이다. JSON으로 보내는 바디데이터와 form 데이터를 구분할 수 있어야 한다.
    이 개념을 전혀 모르고 있으면 나중에 삽질을 만나게 된다. [Content-Type 종류](https://juyoung-1008.tistory.com/4)


![파일을 보내는 경우](../images/Screenshot%202021-07-23%20at%2016.21.05.jpg)

파일과 같은 바이너리 데이터를 보내는 경우 `multipart/form-data` 타입으로 보내면 된다, 각 필드들을 위 이미지처럼 나누어준다. 물론 HTTP 메세지는 브라우저가 자동으로 생성해준다.
개인적 경험으로는 프론트에서 필터와 같은 화면을 만들 때 검색한 옵션들을 쿼리스트링으로 만들어 놓으면 다른페이지로 이동한 뒤 뒤로가기를 해서
돌아오는 경우, 또는 다른사람에게 내가 보고있는 화면을 공유하는 경우에 유리하다, 즉 URL에 쿼리스트링으로 필터정보를 남기면 좋다.

[좋은 URI 설계 개념](http://restfulapi.net/resource-naming)

<br>

