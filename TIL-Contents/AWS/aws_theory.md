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


### IAM 역할
![alt text](../a_images/IAM_role.png)


### EC2의 개념
- 인스턴스
- 컨테이너
- 서버리스
-> //TODO : 인스턴스와 컨테이너의 관계 확인 필요 

- AMI 개념
1) AWS 제공 : Quick Start
2) Marketplace
3) 사용자가 만든 

### EC2 인스턴스 유형
- 범용 : 인스턴스는 컴퓨팅, 메모리, 네트워킹 리소스를 균형 있게 제공하며 
웹 애플리케이션 서버, 컨테이너식 마이크로서비스, 분산 데이터 저장소, 개발 환경 등의 워크로드에 사용할 수 있습니다
- 컴퓨팅 최적화 : 인스턴스는 고성능 프로세서를 활용하는 컴퓨팅 집약적인 애플리케이션에 적합합니다. 과학 모델링, 배치 프로세싱, 분산 분석, 고성능 컴퓨팅(HPC), 기계 학습 등의 워크로드에 이러한 인스턴스를 사용할 수 있습니다.
- 메모리 최적화 : 인스턴스는 메모리에서 대규모 데이터 집합을 처리하는 워크로드를 위한 빠른 성능을 제공합니다. 이러한 인스턴스는 고성능 데이터베이스, 웹 규모의 분산 인 메모리 캐시, 실시간 빅 데이터 분석 등의 메모리 집약적인 애플리케이션에 사용됩니다.
- 가속 컴퓨팅 : 인스턴스는 하드웨어 액셀러레이터 또는 코프로세서(coprocessor)를 사용하여 부동 소수점 수 계산이나 그래픽 처리,데이터 패턴 일치 등의 기능을 기존 CPU를 사용할 때보다 더 효율적으로 수행합니다. 이러한 기능의 예로는 3D 시각화, 그래픽 집약적 원격 워크스테이션, 3D 렌더링, 애플리케이션 스트리밍, 비디오 인코딩 등이 있습니다
- 스토리지 최적화 : 인스턴스는 로컬 스토리지의 대규모 데이터 집합에 대한 순차적 읽기 및 쓰기 액세스 권한이 필요한 워크로드용으로 설계되었습니다. 스토리지 최적화 인스턴스에 적합한 워크로드의 예로는 NoSQL 데이터베이스, 인 메모리 데이터베이스, 확장 트랜잭션 데이터베이스, 데이터 웨어하우징, Elasticsearch, 분석 등이 있습니다. 
- [참고]![alt text](../a_images/EC2_instance.png)

### 인스턴스 수명주기
![alt text](../a_images/EC2_life_cycle.png.png)

IP는 공인 IP가 있는데 public이랑 Elatic으로 나뉨 public은 재부팅 시 새로 발급이 됨

### EC2 요금
- AWS 프리티어
- Saving Plans
- 전용 호스트
- 온디맨드 인스턴스
- 예약 인스턴스
- 스팟 인스턴스
> 이렇게 요금을 정할 수 있는 목록들이 있고
상황에 맞추어 사용자가 적절하게 선택하여 사용하면 된다.

### AWS 컨테이너 서비스

### 가상머신과 컨테이너 비교
![alt text](../a_images/AWS_VM%20vs%20Container.png)

### AWS 컨테이너 오케스트레이션 서비스
- Amazon ECS(Amazon Elastic Container Service)
    - 컨테이너식 애플리케이션 실행 및 크기 조정
    - API 호출을 사용하여 Docker 지원 애플리케이션 제어
- Amazon EKS(Amazon Elastic Container Service)
    - Kubernetes 애플리케이션 실행 및 크기 조정
    - 패치, 노드 프로비저닝, 업데이트 자동화

### 서버리스 컴퓨팅
- 종류 : AWS Fargate, AWS Lambda

[이점]
- 서버를 프로비저닝 또는 관리할 필요가 없음 (근데 사실 어딘가에는 있을거임 but, 관리를 AWS에서 함)
- 사용량에 따른 크기 조정
- 사용한 리소스만큼 비용만 지불
- 가용성 및 내결함성 기본 제공

### Lambda
![alt text](../a_images/AWS_Lambda.png)

- 제한점이 있음 
    - 메모리가 128MB ~ 10GB 
    - 시간 최대 15분 까지 밖에 실행이 안됨
-> cpu는 메모리크기에 준하는 CPU가 자동으로 할당 됨

### Fargate
// TODO : 찾아봐야함

ECS 랑 Fargate 차이
 MicroVM 환경에서 돌아감
 ECS는 EC2 위에서 돌아가는데 기본 가상머신 위에서 돌아감

---

### AWS 네트워킹

#### Amazon VPC [Amazon Virtual Private Cloud]
> VPC를 사용하면 데이터 센터의 기존 네트워크와 유사하게 AWS 클라우드에서 AWS 리소스를 위한 가상 네트워크를 정의하고 프로비저닝할 수 있습니다.

##### VPC 생성 방법 및 서브넷
- 리전 생성
- VPC 이름
- VPC의 IP 주소 범위

![alt text](../a_images/Amazon_VPC.png)
* 사이더 범위? 를 지정해주어야함

###### 서브넷
> VPC 안의 또 다른 범위? 

- 프라이빗 서브넷 / 퍼블릭 서브넷
> 이 둘은 개념의 차이임
    - 퍼블릭 서브넷 : 사설 IP(내부통신) 범위, 공인(외부통신) IP를 모두 할당 받아야함
    - 프라이빗 서브넷 : 사설 IP(내부통신)만 할당되어 저장하는 서브넷

### 클래스 없는 도메인 간 라우팅(CIDR)
![alt text](../a_images/CIDR_Image.png)

### 네트워크 액세스
![alt text](../a_images/Network_Access.png)

### 네트워크 엑세스 제어목록 (NACL)

![alt text](../a_images/NACL.png)


### 보안 그룹 : EC3 인스턴스에 연결된 가상 방화벽
상태저장의 특징이 있음 NACL과의 차이
-> 인/아웃 바운드 규칙에 의해 허용된 세션의 정보를 저장해서 그에 상응하는 리턴 트래픽은 자동으로 허용

### 흐름 로그
VPC 흐름로그 
- 설정을 통한 시간단위로 캡쳐를 함 
- 보여주는 방법이 IP Packet을 잡아줌
- 정말 간단한 트래픽 상황만을 보여줌

---

### AWS 스토리지

#### 유형
- 블록 스토리지
- 파일스토리지 
- 객체 스토리지
![alt text](../a_images/stroage_type.png)