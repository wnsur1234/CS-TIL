# 백엔드 결과 값을 에이전트로..
> 반대로 에이전트 응답 값을 백엔드로...

# 나의 상황
> API 개발은 간단하게 2개 했는데..
일단 Agent랑 연결을 해야함 -> 방법을 알아보자..!!!

### 백엔드와 Agent의 배경

```
Agent 는 BedRock을 사용하고 db는 RDB의 값도 전달 해야하고 DynamoDB도 전달해야해 DynamoDB는 Json 텍스트로 RDB또한 Json의 값
```

### 그랬을때 흐름

1) Spring Boot API에서 데이터 저장/수정 (RDB + DynamoDB)
→ 기존 서비스 로직 그대로 수행

2) 저장/수정이 끝났을 떄 JSON 생성

```
{
  "memberId": 1,
  "trip": { ... },
  "preference": { ... },
  "history": { ... }
}
```
// TODO : 내 프로젝트에서 응답값경우에는?
-> 내 프로젝트의 응답 값
```
{
    "status": 200,
    "message": "날짜 저장성공",
    "data": {
        "id": 1,
        "startDate": "2025-12-15",
        "endDate": "2025-12-19"
    }
}
```
> 이거는 프론트로 가는 응답 값이고

> Agent는 내부의 응답값을 필요로 하기때문에
따로 Json가공을 하는 밑에 createJsonPayload()가 필수로 필요!

3) Spring Boot가 Bedrock Agent Runtime을 직접 호출

- AWS SDK for Java v2 사용 (이미 dependencies 넣으셨습니다)
// TODO : 내가 뭘 이미 넣었어?
-> 답변 : 이게 이미 있다.
```
implementation platform('software.amazon.awssdk:bom:2.29.0')
implementation "software.amazon.awssdk:bedrockagentruntime"
implementation "software.amazon.awssdk:bedrockagent"
```

- 호출 대상은 이것입니다👇
    - InvokeAgent (Bedrock Agent Runtime API)

4) 사용해야하는 API

- Bedrock Agent Runtime 공식 엔드포인트:
    - POST /agents/{agentId}/agentAliases/{agentAliasId}/invoke

> 여기서 앤드포인트 URL을 전체 받을 필요가없음

> agentId, agentAliasId 이 두개의 값만 알면됨

추후에 아래와 같이 저장
```
aws:
  region: ap-northeast-2
  bedrock:
    agent-id: ${BEDROCK_AGENT_ID}
    alias-id: ${BEDROCK_ALIAS_ID}
```


- AWS SDK for Java v2에서는 다음 메서드를 사용합니다:
    - BedrockAgentRuntimeClient.invokeAgent()

5) Spring Boot에서 구현해야 하는 실제 코드 

1. 클라이언트 Bean생성
```
@Bean
public BedrockAgentRuntimeClient bedrockAgentRuntimeClient() {
    return BedrockAgentRuntimeClient.builder()
            .region(Region.AP_NORTHEAST_2)
            .credentialsProvider(
                StaticCredentialsProvider.create(
                    AwsBasicCredentials.create(awsAccessKey, awsSecretKey)
                )
            )
            .build();
}
```

2. Agent 호출 서비스
```
@Service
@RequiredArgsConstructor
public class AgentService {

    private final BedrockAgentRuntimeClient agentClient;

    @Value("${aws.agent.id}")
    private String agentId;

    @Value("${aws.agent.alias-id}")
    private String agentAliasId;

    public String sendDataToAgent(String jsonData) {

        InvokeAgentRequest request = InvokeAgentRequest.builder()
                .agentId(agentId)
                .agentAliasId(agentAliasId)
                .sessionId(UUID.randomUUID().toString())
                .inputText(jsonData)
                .build();

        InvokeAgentResponse response = agentClient.invokeAgent(request); //이게 POST = invokeAgent(request) 역할

        return response.outputText();
    }
}
```

- 사실 여기서의 궁금증 그러면 POST가 아닌다른 요청들은?
    - 답변 : 핵심: Bedrock Agent는 “REST API 서버”가 아니다
    - 특정 메서드(POST·PUT·PATCH)를 직접 설정하는 API가 아님
    - Bedrock Agent는 "invokeAgent" 라는 1개의 API로 모든 상호작용을 처리하는 LLM 기반 Agent임

3. RDB + DynamoDB 저장하는 서비스에서 Agent 호출

```
@Service
@RequiredArgsConstructor
public class TravelDateService {

    private final TravelDateRepository travelDateRepository;
    private final AgentService agentService;

    public Long saveDate(TravelDateReqDto dto) {

        TravelDate travelDate = TravelDate.builder()
                .startDate(dto.getStartDate())
                .endDate(dto.getEndDate())
                .build();

        travelDateRepository.save(travelDate);

        // 👇 1. JSON 생성
        String jsonPayload = createJsonPayload(travelDate);

        // 👇 2. Bedrock Agent 호출
        String agentResult = agentService.sendDataToAgent(jsonPayload);

        System.out.println("Agent Result = " + agentResult);

        return travelDate.getId();
    }

    private String createJsonPayload(TravelDate travelDate) {
        return """
        {
            "rdb": {
                "startDate": "%s",
                "endDate": "%s"
            }
        }
        """.formatted(
                travelDate.getStartDate(),
                travelDate.getEndDate()
        );
    }
}
```

---

### 참고사항!!

- 🔥 3) 세션 유지(대화 유지) → sessionId 사용

POST와 GET을 나누지 않고 세션 기반으로 멀티턴 대화 가능:

String sessionId = "user123-session";

InvokeAgentRequest request = InvokeAgentRequest.builder()
        .sessionId(sessionId)
        .inputText("다음 날 일정도 추천해줘")
        .build();

-> 보통 security기반이라면 로그인 시 sessionID가 생성 되고
    -> 자동생성되어 Header에 담겨 쿠키로 넘긴다.
        -> 로그아웃 시 사라진다.

- 백엔드 관점에서...프로젝트는 JWT 기반이다.
    - 그렇다면 stateless이기 때문에 sessionID가 발급 되지 않는다.
-> 따라서 conversationId or agentSessionId라는걸 만들어 sessionId와 같은 역할을 하는 식별값을 넘겨주어야한다.

### 여기서 궁금점
- 그렇다면 user_id로 고유 식별값이 있으니 이걸로 구별하면 되는거 아닌가?
- user_id만으로 충분한 점
    - Agent가 유저별 맥락을 가져가야 한다” → user_id만으로 충분할 때
    - 즉, 모든것이 user기반으로 구분만 하면될 때
- sessionId와 같은 식별값이 필요할 때
    - Agent가 유저의 대화 맥락을 계속 이어가야 한다” → user_id만으로는 부족함
    - 같은 유저라도 여러개의 대화 세션이 존재
    - 이전 대화 내용과 연결해야함

### 결론
대화 시작 시 
-> `String conversationId = UUID.randomUUID().toString();`와 같이 생성 
-> 
```
{
  "conversationId": "f12a9c33-8d4a-45a2-bdf7-12abce998734",
  "message": "대화가 시작되었습니다."
}
 ```

### 실제 적용

- 대화 시작 api와 일반 대화 api 구분

- 대화 시작 api (POST /conversations/start)
    - conversationId 발급
    - 프롬프트 받음
        - agent 전달


- 일반 대화 api (POST /conversations/{conversationId}/messages)
    - conversationId get
    - 프롬프트 받음
        - agent 전달 


---  

- ✔ EKS + CI/CD는 Bedrock Agent와 아무 충돌도 없다

→ 그냥 백엔드가 어디에 배포되었는지는 상관없이
Spring Boot가 Bedrock Agent Runtime API를 호출하기만 하면 된다.

즉,

EC2 에 있어도 호출 가능

EKS에 있어도 호출 가능

Lambda여도 호출 가능

로컬에서 실행해도 호출 가능

EKS 배포 여부는 전혀 영향을 주지 않는다.


---

### 실제 적용 해보니..

- BedrockAgentRuntimeClient를 사용하지 못하였고
- BedrockAgentRuntimeAsyncClient를 사용하였는데 
    - 둘의 차이점 동기 VS 비동기
    - Async가 실시간 처리가 가능함

> 그래서 Async는 실시간응답이 오는 InvokeAgentResponseHandler를 구현해야함

```
InvokeAgentResponseHandler handler = new InvokeAgentResponseHandler() {

            // 여기의 결과값
            @Override
            public void responseReceived(InvokeAgentResponse response) {
            }

            /**
             * 시나리오
             * 1. 실제 응답 = ResponseStream이고
             * 2. instanceof 에서 PayloadPart(=chunk)타입만 저장
             * @param stream Agent 응답 조각
             */
            // 또 여기 결과값
            @Override
            public void onEventStream(SdkPublisher<ResponseStream> stream) {
                stream.subscribe(event -> {
                    if (event instanceof PayloadPart payload) {
                        SdkBytes bytes = payload.bytes();
                        String text = new String(bytes.asByteArray(), UTF_8);
                        finalText.append(text);
                    }
                });
            }

            // 또 여기 결과값
            @Override
            public void exceptionOccurred(Throwable throwable) {
                resultFuture.completeExceptionally(throwable);
            }

            // 을 최종적으로 모아서 답변
            @Override
            public void complete() {
                resultFuture.complete(finalText.toString());
            }
        };
```

### 중점적으로 봐야할 point

- Handler에서 ResponseStream에 Agent의 조각 응답들이 담김
- instanceof를 통해 그 응답의 type을 결정하는데
    - type은 chunk부터 엄청많음
    - 근데 우리가 필요한건 Chunk타입이고
    - chunk = PayloadPart 이다.
    - 그걸 text에 모으고
- final text에 저장

- 마지막으로 CompletableFuture<String> 으로 반환한다. 

### CompletableFuture<String> 란? 
> “비동기 작업의 결과를 미래에 받겠다는 약속(Promise)”

- String = 나중에 만들어질 실제 최종 응답 값
- CompletableFuture<String> = 그 값을 나중에 넣어서 돌려줄 수 있는 그릇

Agent의 응답은 스트리밍 기반이고 바로 리턴되지 않기 때문에,
즉시 String을 반환할 수 없고
나중에 complete() 되는 시점에 값을 반환하도록 미래 객체(Future)를 사용