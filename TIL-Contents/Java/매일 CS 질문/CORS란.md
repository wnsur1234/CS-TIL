# CORS 란 무엇인가요?

## 내 답변 

: CORS란 백엔드 애플리케이션 서버와 프론트를 통한 도메인(DNS)의 관의 관계 설정을 말합니다.
대표적으로 JAVA에서 Spring Security를 구현할 때 설정하게 되는데 
CORS가 기본적으로 able 즉, 사용가능하도록 되어있습니다. 이게 뜻하게 되는것은
DNS가 HTTPS 상에 올라가있을때 백엔드 서버는 보통 별도의 설정이 없다면 Http상으로 올라가게됩니다.
이렇게 HTTPS와 HTTP상 호환을 해주는 설정을 CORS라고합니다.
다만, 이걸 설정해놓았을 시 HTTPS가아닌 HTTP가 접근하게됨으로 보안에 취약해질 가능성이 높습니다.

---

## Chat GPT 답변

전체적으로 **“프론트/백엔드가 서로 다른 출처(origin)일 때 접근을 제어한다”**는 핵심이 빠져 있고,<br>
대신 HTTPS↔HTTP 호환이나 DNS 관계 쪽으로 설명이 많이 틀어져 있어서 오답에 가깝습니다.<br>
아래처럼 고치시면 면접/스터디 답변으로 바로 써도 됩니다.

#### 무엇이 정확히 아쉬운지(틀린 포인트)

-  CORS는 DNS 관계 설정이 아닙니다
    - CORS는 브라우저 보안 정책(SOP: Same-Origin Policy) 때문에 생기는<br> “교차 출처 요청을 허용/차단하는 규칙”입니다.
    - 기준은 Origin(스킴+호스트+포트) 입니다. (DNS 자체가 핵심이 아님)

- “HTTPS와 HTTP 호환을 해주는 설정”이 아닙니다
    - HTTPS↔HTTP는 “호환” 문제가 아니라 출처가 달라지는 요인 중 하나일 뿐입니다.
    - 예) https://a.com → http://a.com 은 스킴이 달라서 다른 Origin 입니다.
    - CORS의 본질은 서버가 특정 Origin에 대해 응답 헤더로 허용 여부를 알려주는 것입니다.

- “CORS가 기본적으로 able”은 표현이 반대 느낌
    - 브라우저는 기본적으로 다른 Origin의 요청을 제한합니다.
    - 서버(Spring 등)는 “기본 허용”이라기보다,<br> 
    설정이 없으면 필요한 CORS 헤더를 안 붙여서<br> 
    브라우저가 막는 경우가 많다가 더 정확합니다. (구성/환경에 따라 다름)

- 보안 설명이 방향이 틀어짐
    - “CORS 설정하면 HTTP가 접근해서 취약” ❌
    - CORS는 허용할 Origin을 좁게 설정하면 오히려 안전합니다.
    - 주의점은 “HTTP 접근”이 아니라 *(모든 Origin 허용), 과도한 허용,<br>
    credentials(쿠키/인증) 허용 조합 같은 설정 실수입니다.

### 정답에 가까운 답변 예시(짧고 정확)
**CORS(Cross-Origin Resource Sharing)**는 브라우저의 Same-Origin Policy로 인해<br>
다른 출처(스킴/도메인/포트가 다름)로 보내는 요청이 제한될 때,<br> 
서버가 응답 헤더(Access-Control-Allow-Origin 등)로 어떤 출처의 요청을 허용할지 명시하는 메커니즘입니다.<br>
브라우저는 경우에 따라 preflight(OPTIONS) 요청으로 서버가 허용하는지 먼저 확인하고,<br>
허용되지 않으면 응답을 받아도 JS에서 접근을 막습니다.<br>
보안적으로는 필요한 Origin만 허용하고, 쿠키/인증이 필요한 경우<br>
Allow-Credentials와 Allow-Origin을 안전하게 조합하는 것이 중요합니다.


### 지금 답변을 최소 수정해서 “살리는” 방향(당신 문장 기반)
- “DNS 관계 설정” → “Origin(스킴/도메인/포트) 기준”
- “HTTPS↔HTTP 호환” → “스킴이 다르면 Origin이 달라져 CORS 이슈가 발생할 수 있음”
- “CORS 기본 able” → “브라우저는 기본적으로 차단, 서버가 헤더로 허용해줘야 함”
- “HTTP 접근해서 취약” → “허용 범위를 넓히면 취약, 필요한 Origin만 허용해야 함”

---

## 매일 메일 답변

ORS(Cross Origin Resource Sharing)는 출처가 다른 곳의 리소스를 요청할 때 접근 권한을 부여하는 메커니즘입니다.<br> 
리소스를 주고받는 두 곳의 출처가 다르면 출처가 교차한다고 합니다.<br>
이때 출처는 URL뿐만 아니라 프로토콜과 포트까지 포함됩니다.<br> 
만약 클라이언트의 출처가 허용되지 않았다면 CORS 에러가 발생할 수 있습니다.

---

### CORS는 왜 필요한가요?
과거에는 `크로스 사이트 요청 위조(CSRF, Cross-Site Request Forgery)` 문제가 있었습니다.<br>
피해자의 브라우저에서 다른 애플리케이션으로 가짜 클라이언트 요청을 전송하는 공격입니다.<br>

CSRF를 예방하기 위해 브라우저는 `동일 출처 정책(SOP, same-origin policy)`을 구현했습니다.<br>
`SOP`가 구현된 브라우저는 클라이언트와 동일한 출처의 리소스로만 요청을 보낼 수 있습니다. <br>
하지만, SOP는 한계가 있습니다.<br> 
현대의 웹 애플리케이션은 다른 출처의 리소스를 사용하는 경우가 많기 때문입니다.<br>
따라서, `SOP를 확장한 CORS가 필요`합니다.

---

### CORS는 어떻게 작동할까요? 🤔
브라우저가 요청 메시지에 Origin 헤더와 응답 메시지의 Access-Control-Allow-Origin 헤더를 비교해서 CORS를 위반하는지 확인합니다. <br>
이때, `Origin`에는 현재 `요청하는 클라이언트의 출처(프로토콜, 도메인, 포트)`가,<br>
`Access-Control-Allow-Origin`은 `리소스 요청을 허용`하는 출처가 작성됩니다.
<br>

이렇게 단순하게 요청하는 것을 `Simple Request`라고 합니다.<br>
`Simple Request`은 `요청 메서드(GET, POST, HEAD)`,<br>
`수동으로 설정한 요청 헤더(Accept, Accept-Language, Content-Language, Content-Type, Range)`,<br> 
`Content-Type 헤더(application/x-www-form-urlencoded, multipart/form-data, text/plain)`인 경우에만 해당합니다.<br>

브라우저가 `사전 요청`을 보내는 경우도 있습니다.<br>
이때 사전 요청을 Preflight Request라고 합니다.<br> 
브라우저가 본 요청을 보내기 이전, `Preflight Request`를 `OPTIONS` 메서드로 요청을 보내어 실제 요청이 안전한지 확인합니다.<br>

`Preflight Request`는 추가로 `Access-Control-Request-Method`로 실 요청 메서드와,<br> 
Access-Control-Request-Headers 헤더에 실 요청의 추가 헤더 목록을 담아서 보내야 합니다.<br>

이에 대한 응답은 대응되는 `Access-Control-Allow-Methods`와 `Access-Control-Headers`를 보내야 하고,<br> 
`Preflight Request`로 인한 추가 요청을 줄이기 위해 `캐시 기간`을 `Access-Control-Max-Age`에 담아서 보내야 합니다.<br>

또한 `인증된 요청을 사용하는 방식`도 있는데요. 이를 `Credential Request`라고 합니다.<br>
쿠키나 토큰과 같은 인증 정보를 포함한 요청은 더욱 안전하게 처리되어야 합니다.<br> 
이때 `Credential Request`를 수행합니다.<br>

`Credential Request`를 요청하는 경우에는<br>
서버에서는 `Access-Control-Allow-Credentials`를 `true`로 설정해야 하며<br> 
`Access-Control-Allow-Origin`에 `와일드카드`를 <u>사용하지 못합니다.<u>