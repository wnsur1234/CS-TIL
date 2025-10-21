# AWS
**:book: Contents**
* [Cloud](#클라우드-컴퓨팅)
* [이점](#클라우드-컴퓨팅의-이점)

---
### 클라우드 컴퓨팅
> 클라우드 컴퓨틩은 종량제 요금으로 인터넷을 통해 IT 리소스를 온디맨드로 제공하는것
조금 더 쉽게 이야기하자면 사용자가 EC2를 통해 여러가지 방법(ex)콘솔(GUI),명령어)을 가지고 Data Center에 접근하여 프로비저닝(=배포)하여 그 사용한 만큼만 요금을 지불하는것

이런 클라우드 컴퓨팅 서비스중 하나는 `AWS`가 있는 것

* 찾아보기 : 온프레미스 환경, 온디맨드 / 솔직히 걍 클라우드 컴퓨팅에 대한 블로그글 찾아봐야할듯..

### 클라우드 컴퓨팅의 이점
- 종량제 : 내가 사용한 만큼만 비용지불
- 거대한 규모의 경제로 얻게 되는 이점
- 용량 추정 불필요
- 속도 및 민첩성 향상
- 비용 절감 목표 실현
- 몇 분 만에 전 세계에 배포

### AWS의 다양한 서비스
> 클라우드 컴퓨팅 서비스중 AWS는 광범위한 서비스를 제공해준다.
![alt text](../a_images/aws_service.png)

---

###  AWS GLobal Infrastructure
> 데이터 센터, 네트워킹 연결 등의 인프라는 모든 클라우드 애플리케이션의 기초가 되는요소입니다. 이러한 물리적 인프라로 구성되어 있는 AWS의 인프라가 AWS Global Infrastructure입니다. 여기에는 `리전,가용 영역, 엣지 로케이션이` 포함됩니다.

### 보충자료
![alt text](../a_images/data_center_image.png)


### 배포시 리전 선택
- 지연시간 : 가능한 고객의 위치와 가까운 리전을 선택 -> 빠르게 컨텐츠 제공
- 요금 : 나라마다 비용이 다르기때문에 리전별로 비용이 상이
- 서비스 가용성 : 일부 리전에서만 사용 가능한 AWS 서비스도 존재
- 데이터 규정 준수 : 법적요건, 나라,회사의 규정등을 따라야한다.

`위 항목들을 고려하여 리전을 선택하여야한다.`

### 가용영역

### 엣지 로케이션
CDN 서비스 -> cloud Front
DNS -> Route53
보안(Ant:DDos) -> shield

---
### AWS 접근 방법
- AWS Management Console (AWS 관리 콘솔) - Id/Password
- AWS Command Line Interface (AWS 명령줄 인터페이스, AWS CLI) - Access Key ID/ secret AccessKey
- AWS software development kit (AWS 소프트웨어 캐발 키트, AWS SDK) - Java,파이썬

### 공동 책임 모델 다이어그램
> 고객이 AWS 제품 및 서비스를 사용할 때 보안을 책임지는 주체는
AWS회사와 고객 둘다인데 관련하여 아래 사진을 참고 하면 좋을것 같다.
![alt text](../a_images/Aws_component_diagram.png)


### IAM(AWS Identiy and Access Management)
* [참고]![alt text](../a_images/introduce_IAM.png)

### IAM 기능
- 전세계에서 사용가능
- AWS서비스와 통합됨
- AWS 계정에 대한 공유 엑세스 (멀티 계정)
- Multi-Factor Authentication(MFA)
- 자격 증명 연동
- 무료로 사용

![alt text](../a_images/IAM_ability.png)
