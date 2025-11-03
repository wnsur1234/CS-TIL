# 쿠키에 관하여...
> Cookie와 로그인 🍪(`HttpOnly`와 `Secure Cookie` 그리고 `withCredentials`)

### Cookie란?
> 쿠키란 서버가 사용자의 웹 브라우저에 전송하는 작은 데이터 조각

- 브라우저는 그 데이터 조각들을 저장해 놓았다가, 동일한 서버에 재요청 시 저장된 데이터를 함께 전송한다.
- 쿠키는 두요청이 동일한 브라우저에 들어왔는지 아닌지를 판단할 때 주로 사용한다.
    - 이를 이용하면 사용자의 로그인 상태를 유지할 수 있다.
        - 상태가 없는 http 프로토콜에서 상태 정보를 기억 시켜주기 때문 

> 여기서 동일한 브라우저에 두 요청이 있다는데 이게 뭔소리지?

[답변] 
#### 📘 시나리오
당신이 크롬 브라우저로 https://example.com에 로그인합니다.
서버는 응답으로 이런 쿠키를 내려보냅니다:
```
Set-Cookie: sessionId=abc123; Path=/; HttpOnly
```
이 쿠키는 당신의 크롬 브라우저에 저장됩니다.
#### 🟢 이후 상황:
당신이 같은 크롬 브라우저로 https://example.com/profile에 접근하면
크롬은 자동으로 요청 헤더에 아래 쿠키를 붙여서 보냅니다:
```
Cookie: sessionId=abc123
```
#### ✅ 서버는 이 sessionId를 보고:
“아, 이 요청은 로그인한 사용자 abc123 세션에서 왔구나!”
→ 로그인 상태 유지 가능!

> “동일한 브라우저에서 두 요청이 왔다”는 말은,
같은 브라우저에 저장된 쿠키가 자동으로 요청에 포함되었다는 뜻입니다.
→ 덕분에 서버는 사용자 식별이 가능하고, 로그인 상태 유지도 가능한 거예요.

`즉, 같은 브라우저에서 같은 요청을 할 때를 말함`

#### ✅ 그래서 로그인 유지가 가능한 이유?
- HTTP는 **무상태(stateless)**이지만
- 쿠키를 통해 "누구"인지 추적 가능하기 때문에
- 서버가 상태처럼 행동할 수 있는 것!

### 쿠키를 언제 사용하면 좋을까
- 세션 관리
    - 서버에 저장해야 할 로그인, 장바구니 등의 정보 관리
-> ex) 세션에 저장되는 id값을 쿠키에 담는다
- 개인화
    - 사용자 선호, 테마 등의 세팅
- 트래킹
    - 사용자의 행동을 기록하고 분석하는 용도

### 쿠키는 어떤 특징이 있을까?
1. http 통신을 한다면 쿠키를 주고 받을 수 있다.

2. 같은 도메인이라면 header 에 자동으로 쿠키가 담겨서 보내진다.
    - 같은 도메인이라면 서로 다른 scheme일지라도 쿠키를 공유할 수 있다.
-> 여기서 뜻하는 도메인이란? 앤드포인트를 말하는건지? 아니면 같은 탭을 말하는건지?

[답변]
> 🧠 “도메인” = URL 중 호스트명 부분을 의미합니다

```
https://gptonline.ai/ko/learn?id=123
        └────────────┬────────────┘
                 이것이 도메인 (gptonline.ai)
```
 
- 도메인(gptonline.ai, naver.com, example.org 등)
- 서브도메인 (www.naver.com, api.naver.com) -> 다르면 기본적으로 안 됨 (a.example.com ≠ b.example.com)
- 포트 (:80, :443 등) -> 무시됨 (example.com:80 == example.com:3000)
- 프로토콜 (http/https) -> 기본적으로 무시됨 (http/https) 하지만 secure 속성이 있으면 https만 허용

이 url에는 정보가 담기는데 이가 같은 것을 말함


3. 서버에서도, 클라이언트에서도 생성/접근/관리할 수 있다.

4. http와 https 사이에도 쿠키를 교환할 수 있다.
    - secure 접미사를 사용하면 https 에서만 쿠키를 전송하도록 설정할 수 있다.

### 쿠키를 만들어보자!
- HTTP 요청을 수신할 때, 서버는 응답과 함께 `Set-Cookie header` 를 전송할 수 있다. 
-> 서버 --> 헤더 --> 브라우저 --> 쿠키 저장
- 쿠키는 보통 `브라우저에 의해` 저장된다.
- 쿠키는 같은 서버에 의해 만들어진 요청들의 `Cookie HTTP header` 안에 포함되어 전송된다.
-> 저장된 쿠키정보 --> 쿠키 헤더를 포함하여 --> 서버
- 만료일과 지속시간을 명시할 수 있다.
    - 만료된 쿠키는 더 이상 보내지지 않는다.
- 특정 도메인 혹은 경로를 제한할 수 있다.

간단한 예시)
간단한 쿠키는 다음과 같이 설정될 수 있다.
```
Set-Cookie: <cookie-name>=<cookie-value>
```
이 서버 헤더는 클라이언트에게 쿠키를 저장하라고 전달한다.
```
# 서버 헤더
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry
```
이제 서버로 전송되는 모든 요청과 함께, 브라우저는 Cookie header 를 사용하여 서버로 이전에 저장했던 모든 쿠키들을 회신한다.
```
# 브라우저
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

---

### 쿠키의 라이프 타임 😇
> 쿠키의 라이프 타임은 두 가지 방법으로 정의 할 수 있다.

- 세션 쿠키 (session cookie)
    - Expires(유효 일자)나 Max-Age(만료 기간) 옵션이 지정되어있지 않아서 브라우저가 닫힐 때 함께 삭제된다.
    - 재시작할 때 세션을 복원해 세션 쿠키가 무기한 존재할 수 있도록 하기도 한다.
- 영속적인 쿠키
    - Expires 속성에 명시된 날짜에 삭제되거나,Max-Age 속성에 명시된 기간 이후에 삭제된다.
    - 브라우저를 닫아도 쿠키가 삭제되지 않는다.
> Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;

### 보안이 중요한 이유 (HttpOnly와 Secure Cookie의 탄생)
1. XSS
    - 클라이언트 브라우저에 JavaScript 를 삽입해 실행하는 공격
        - 공격자의 코드가 내 사이트의 로직인 척 행동할 수 있다.
2. CSRF
    - 다른 사이트에서 우리 사이트의 api 콜을 요청해 실행하는 공격
        - 로그인한 척 계좌 비밀번호를 바꾸거가 송금을 보낸다.

> 위 처럼 쿠키는 Client에서 JavaScript 로 조회할 수 있기 때문에 
 보안 설정을 하지 않은 채로 통신을 해버리면 쿠키를 가로챌 수 있다는 문제가 있다.
 그렇기 때문에 클라이언트와 서버에서 여러가지 방법을 이용해서 이런 공격들은 방어해야 한다.
 그 방법들에 대해서 알아보자.

---

## HttpOnly 와 Secure Cookie

- HttpOnly
> HttpOnly 쿠키 속성을 사용하면 `JavaScript를 통해 쿠키에 접근할 수 없게` 되어, 악성 스크립트를 통해 쿠키 값에 접근하는 것을 막아준다.

HTTP Only Cookie 를 활성화 하기 위해서는 쿠키를 생성할 때, `가장 마지막에 HttpOnly 라는 접미사`를 추가하면 된다.

- Secure Cookie
> HTTP Only Cookie를 사용하면 Client에서 Javascript를 통한 쿠키 탈취문제를 예방할 수 있다.
하지만 Javascript가 아닌 네트워크를 직접 감청하여 쿠키를 가로챌 수도 있다.
그렇기 때문에 이러한 정보 유출들을 막기 위해, HTTPS 프로토콜을 사용하여 데이터를 암호화하여 서버에 넘겨주게 되면, 해커들이 쿠키를 탈취해도 암호화가 되어있어 정보를 알아낼 수 없다.

이를 위해 `Secure 접미사`를 사용해서 쿠키를 생성하게 되면 브라우저는 `HTTPS 가 아닌 통신`에서는 쿠키를 전송하지 않는다.
HttpOnly 속성과 Secure 속성을 사용해 생성한 쿠키의 예시이다.

```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```

### 🩻 위 두가지를 사용할 때 각 주의점

[httponly]
- HttpOnly를 쓰면 JS에서 이 쿠키를 읽을 수 없으므로, 프론트엔드에서 꺼내서 헤더에 담는 식의 토큰 사용이 불가능
    - 예: RefreshToken을 HttpOnly로 저장하면, JS에서는 절대 접근 못 함 
        → 자동 쿠키 전송 구조를 사용해야 함 (withCredentials: true)
[secure]
- Secure를 쓰면 HTTPS 환경이 아닌 경우 쿠키가 전송되지 않음 → 개발 환경에서도 HTTPS 써야 테스트 가능

---

위 정리 내용은 
[cookie 블로그](https://velog.io/@eeeve/HttpOnly%EC%99%80-Secure-Cookie)를 [참조] 하였습니다.
