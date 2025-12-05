# 실전

IAM

1) user
    - ManageMentConsle : ID/PW
    - AD2(AWS CLI,SDK) : AccessKey/SercetAccessKey
2) group : User의 묶음

3) policy : Authorization 설정
    - 권한, 사용자, 그룹한테 정책을 부여할 수 있음
    - Managed : 1:N 방식
    - inline : 1:1 방식
        - 사용자 : 특정 사용자 입장에서 권한을 부여하는것
        - 리소스 : `서비스마다` 이용가능한 사용자를 설정함 ex) S3,SQS,SNS,Lambda 등
        - 리소스권한이 높다 | 권한 충돌이 일어날 수 있음
        - //TODO : 리소스권한? 저건 어떤 상황에서 주는건지
4) Role (!!매우 중요!!)
    - 사용자에게 할당할 수 있지만, 그룹에는 부여할 수 없음
    - Role은 모자로 표현을 함
    - case1 : 사용자에게 `임시적으로` 권한을 부여
        - 시나리오 : 개발유저를 만들었음 -> DEV 환경에 들어감 -> 근데 Prod 에도 들어가야함 -> 그냥 policy부여를하면되지만
            -> 최소권한에 어긋남 -> 따라서 두번째 방법으로 produser를 만듬 -> 근데 이것도 위험하고 여러 문제가 존재
            -> 그래서 세번째 방법 ->  dev그룹과 prod그룹을 만들어서 필요할때 dev사용자가 prod그룹으로 잠시 바꿈
            -> 이것또한 큰 문제가 존재
            -> 따라서 최종적으로 Role 방식 존재 -> policy에서 role(모자)에게 권한을 줌 -> dev개발자가 prod서버 들어가려면
            -> 모자(role)을 쓴다 -> 이러면 dev때의 권한 잠시 없어짐 -> prod에서 작업을하고 돌아오면 다시 권한 돌아옴
    - 권한 정책과 신뢰정책을 설정해줘야함
    - 권한 정책 -> what? -> policy를 말함 즉, 어떤 권한을 줄꺼니?
    - 신뢰 정책 -> who? -> 누가 이 모자를 쓸건지를 정해줘야함
    - STS 감춰저 있는 서비스임
        - 얘가 token,ak,sak키를 발급해줌
        
