# 예외 처리 실전

### 이전까지....

```
사실 이전까지 예외 처리를 하면서
다른 팀원 또는 부트캠프에서 common_exception으로 만든 
예외 처리 및 api시 success응답 또한
그냥 썼었다.. 매번 gpt 한테 물어보며..
```

### 그러나!!
> 이번에 오르미캠프를 하며 진짜 0에서부터 시작하다보니...

이런 응답 구조 통일화, 예외처리를 직접 구조를 잡고 하며
좀 정확하게 확실히 알게 된것 같다.

---

### 실전
> 먼저 구조이다.

```

   ├── common

   │   ├── dto

   │   │   └── BaseResponse.java

   │   └── exception

   │       └── ErrorCode.java
   
   │       └── CustomException.java

   │       └── GlobalExceptionHandler.java

```

> 각 기능에 대해 설명하자면

- BaseResponse (응답의 최종 틀)
    - Success와 Error 모두 컨트롤러에서 api 응답을 할때
    - [Header,body] 형식의 Json 응답을 지정하는것
    - 보통 body만 custom하는데 status,code,message 등을 지정한다.

- ErrorCode
    - 보통 에러는 300, 400, 500 으로 크게 분류 되는데
    - 이런 오류들을 service나 api로직에서 응답이 발생될 것 같은 지점에
    - 예외처리를 하는데 있어 원하는 응답 code와 message를 반환 하기 위해서이다.
    - `프로젝트 코유의 오류 이름을 사용 가능`

-> `CustomException과 GlobalExceptionHandler 얘네 둘의 역할을 잘 몰랐음!`

- CustomException 
    - RuntimeException을 상속받아
    - ErroCode에서 지정한 에러 형태를 꺼낸다
    - code와 message등을 꺼낼 수 있다.
    - 근데 얘만 만든다고 쓸 수는 없음

- GlobalExceptionHandler (사실상 이녀석이 제일 중요)
    - 이 Handler로 db,dto,customException을 통한
    실제 api 응답 형식에 추가를 한다.
    - @ExceptionHandler(CustomException.class) 어노테이션을 활용해
    실제 api 로직에서 CustomException을 사용하면 
    위에 정했던 응답 형식들이 success때와 마찬가지로 응답하게 된다.
    - 지금 찾아보니 Handler에서 만든게 어떻게 
    메서드를 활용하지 않았는데도 사용이 되나 매우 궁금했는데
    - `@RestControllerAdvice`라는 어노테이션을 통해 자동으로 
    `@ExceptionHandler(CustomException.class)`찾는것


### 그전에 이전까지의 프로젝트에서는,.,.,.
> 이전까지의 프로젝트에서는 exception 폴더에 너무 많은 에러관련
파일들이 있었음 

[그림 참고]

![alt text](../../../a_images/common_exception.png)

- 위 그림에서 지금까지 설명한것 제외 설명
    - ErrorInfo
        -  BaseResponse 안에서 “error” 객체를 구성하는 상세 필드 (타임스탬프, path, status 등)

    - ApiErrorResponseCustomizer + @ApiErrorResponses
        - Swagger 문서에 자동으로 Error 예시를 넣어주는 기능
        (실제 API 실행과는 관계 없음 / 문서 생성용)