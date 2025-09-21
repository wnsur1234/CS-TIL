# 회원가입/로그인 기본 흐름
**:book: Contents**
* [들어가기전](#배경지식)
* [로그인/인증](#로그인인증-방식)

## 배경지식
> :arrow_double_up:[Top](#회원가입로그인-기본-흐름)   :leftwards_arrow_with_hook:[Back](https://github.com/wnsur1234/CS-TIL#CS)   :information_source:[Home](https://github.com/wnsur1234/CS-TIL)

```
`회원가입`은 단순히 
사용자가 입력한 정보(이메일,비밀번호,이름 등)를 DB에 저장하고
저장하기 전 중복체크 -> 비밀번호 암호화의 방식으로 존재하고
다만, `소셜(Oauth2)기반` 회원가입 or `일반 회원가입` 정도로 구분된다.
```
그에 반해 (로그인/인증) 여기서 여러가지로 방식이 나뉜다.
*핵심 개념은 "로그인 후, 서버가 사용자의 인증 상태를 어떻게 유지할 것인가?" 이다.

### 용어 정리
### 인증(Authentication) vs 인가(Authorization)

| 구분 | 인증(Authentication) | 인가(Authorization / 권한) |
|------|----------------------|-----------------------------|
| 의미 | "누구냐" (로그인)     | "무엇을 할 수 있냐" (접근 권한) |
| 예시 | 로그인 성공 → 사용자 확인 | 관리자만 삭제 가능 같은 정책 |
| 관련 기술 | 로그인, JWT 발급 | 권한 체크, 역할(Role) 판별 |

---
### [로그인/인증 방식]
> :arrow_double_up:[Top](#회원가입로그인-기본-흐름)   :leftwards_arrow_with_hook:[Back](https://github.com/wnsur1234/CS-TIL#CS)   :information_source:[Home](https://github.com/wnsur1234/CS-TIL)
1. session(세션)
    - 서버가 메모리에 `세션 ID`를 저장
    - 클라이언트는 `JSESSIONID` 쿠키로 서버와 통신
    - 전통적인 Srping Security 기본 방식
2. JWT(JSON Web Token)
    - 서버는 상태를 저장하지 않고, 클라이언트가 토큰을 보관
    - 로그인 성공 시 JWT 발급 -> 이후 요청마다 JWT 헤더에 담아서 보냄
    - 서버는 토큰 검증만 함 -> 확장성이 좋음 (분산 서버, 모바일 앱 등)
3. Oauth2.0
    - 구글,카카오,네이버 같은 외부 인증 서버를 활요
    - 사용자가 구글 계정으로 로그인 -> 구글이 인증해주고 우리 서버는 사용자 정보만 받음
    - 우리 서버는 자체 비밀번호 DB가 아니라 외부 프로바이더에 의존

이렇게 3가지 `로그인/인증 방식`이 존재하고 
각각의 방식들을 좀 더 자세하게 공부해 보겠다.

### session
> :arrow_double_up:[Top](#회원가입로그인-기본-흐름)   :leftwards_arrow_with_hook:[Back](https://github.com/wnsur1234/CS-TIL#CS)   :information_source:[Home](https://github.com/wnsur1234/CS-TIL)
* [▶️답변내용](https://github.com/wnsur1234/CS-TIL/Java/spring/security_basic.md)

### JWT
> :arrow_double_up:[Top](#회원가입로그인-기본-흐름)   :leftwards_arrow_with_hook:[Back](https://github.com/wnsur1234/CS-TIL#CS)   :information_source:[Home](https://github.com/wnsur1234/CS-TIL)
* [▶️답변내용](https://github.com/wnsur1234/CS-TIL/Java/spring/jwt_quthentication.md)
