# Developing Generative AI Applications on AWS 1일차

### BedRock 약간 어제 복습
> 이는 AWS에서 제공하는 AI 활용 서비스

실상 사용자가 어떠한 API 요청을 하면 바로 그 모델에 접근할 수 있는것이 아닌
무조건 BedRock을 통해 사용된다.

-> 약간 API GateWay같은 역할을 함

[그러나]
`Sagemaker` 을 사용하면 OpenAI를 바로 사용가능함

![alt text](../../../a_images/bedRock_example.png)

물론 둘다 https를 통해 호출을 하는 것

### FM(Foundation Model) 사전 훈련

흐름 : 대량의 레이블 미지정 데이터 -> 훈련 알고리즘 -> 파운데이션 모델
