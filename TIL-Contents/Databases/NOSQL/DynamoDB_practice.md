# DynamoDB 실전

### 배경
```
실제 내 프로젝트를 AWS를 적용하여 시작하다 보니 
DynamoDB를 연결해야하는 일이 발생되었다.
그러나 지금 미리 연결을 한다고 하면
리소스가 띄어지기때문에 비용이 발생하기 때문에 어떻게 하지?
라고 고민하던 찰나@!!!

내가 AWS 수업을 들으면서 DynamoDBLocal을 통해
Local에서만 AWS와 연결하지 않고 실행되는 방법이 있다는 것을 찾았다!
```

그렇다면 어떻게 적용하는지 보니
다운로드 이후에는 정확한 설명이 `공식문서`에는 나오지 않는거 같아
블로그를 뒤적여본다...

### DynamoDB의 연결방법 2가지

- Docker 이미지로 실행
    - 이거는 [공식사이트](https://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html) 여기에 잘 나와있다고함
    - 약간 Redis를 docker에 올려서 컨테이너 실행하고 프로젝트 실행하는거랑 동일

- NoSQL WorkBench 다운로드

### Docker 이미지 방식
> 간단하게 

---

```
services:
  dynamodb-local:
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data -port 18200"
    image: "amazon/dynamodb-local:latest"
    ports:
      - "18100:18100"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
    restart: always
    networks:
      - dynamodb-network

  dynamodb-init:
    image: amazon/aws-cli:2.13.25
    environment:
      AWS_ACCESS_KEY_ID: DUMMYIDEXAMPLE
      AWS_SECRET_ACCESS_KEY: DUMMYEXAMPLEKEY
      AWS_DEFAULT_REGION: ap-northeast-2
    volumes:
      - "./docker-dynamo-init.sh:/docker-dynamo-init.sh"
    entrypoint: [ ]  # 기본 entrypoint 제거
    command: [ "sh", "/docker-dynamo-init.sh" ]
    depends_on:
      - dynamodb-local
    networks:
      - dynamodb-network

networks:
  dynamodb-network:
    driver: bridge
```

### WorkBench 다운로드
> DynamoDB 용 NoSQL WorkBench는 AWS에서 제공하고있습니다.

이것도 그냥 블로그 따라하면 됨

[참고사이트](https://hansunsin.tistory.com/15)
