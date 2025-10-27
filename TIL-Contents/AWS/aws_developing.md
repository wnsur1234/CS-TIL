### Developing on AWS 1일차

### 수업 개요
- python을 통해 진행
- 3일 이론 4일 실습
- Jam을 통해 문제 상황 접했을 시 해결하는 수업
- 5일차에 Jam이 한번 더 있음
- 수업은 서버리스 위주로 진행이 됨
- 수업은 게시판을 목표로 진행될꺼고 백,프론트엔드 코드는 제공해줌 | 그 사이사이 코드만 함
 
---

### 애플리케이션 구축
![alt text](../a_images/app_structure.png)

---

### AWS에서 개발 시작하기

### AWS 서비스에 프로그래밍 방식으로 액세스

[AWS REST API]
> Rest API 방식에서 요청과 응답을 하는데 AWS도 마찬가지로 HTTP를 통해 소통한다.

HTTP 상태 코드
- 100 시리즈
    - Informational
- 200 시리즈
    - Success
- 300 시리즈
    - Redirection
- 400 시리즈
    - Client error
- 500 시리즈
    - Server error

> 또한 SDK 방식으로 진행이 된다. 즉 코드로 진행이 됨 (콘솔은 거의 사용하지 않는다.)

### SDK를 사용하는 이유
- 언어 바인딩
- HTTP 요청 서명
- 기본 탑재하고 있는 복원력 관련 기능
- 재시도/오류/시간 제한 로직
- 페이지 매김(Pagination) 지원

### SDK를 사용하는데 고려사항
[사용할 SDK API 결정]
- 하위 수준 API
    - 서비스 작업당 메서드가 1개있음
- 상위 수준
    - 개념적 리소스당 1개의 클래스가 있음
    - 서비스 리소스와 개별 리소스를 정의

=>  둘을 거의 같이 사용하긴함

### CLI 문법
> AWS CLI는 Botocore라는 Python 라이브러리를 사용합니다. AWS Python SDK 
Boto3 라이브러리는 Botocore를 기반으로 합니다. AWS 명령줄 호출의 기본 구조는 aws 명령과 그 뒤에 오는 다음 부분으로 구성됩니다. 

![alt text](../a_images/AWS_CLI.png)

### SDK 및 프로그래밍 패턴
> 사실 실습으로 진행하는게 좋음

- 서비스 작업
    - JSON 형태로 진행이됨
- 비동기식 작업
