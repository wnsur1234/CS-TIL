* [참고](#출처)

### 들어가기전

```
AWS는 강력합니다. 하지만 그만큼 복잡하죠.매번 문서를 뒤적이며 설정을 반복하고, 오류를 디버깅하는 데 시간을 낭비하고 있다면, 이런 생각이 들 수도 있습니다.

“AI가 AWS 설정도, 문서도 알아서 처리해줬으면 얼마나 좋을까…”

이런 니즈를 해결하기 위해 등장한 것이 바로 AWS MCP Servers입니다.
AI 기반 개발 환경, 문서 검색, 인프라 자동화, 비용 분석 등 다양한 AWS 작업을 AI와 연결된 서버 구조로 간단히 처리할 수 있게 돕는 도구입니다.
이 글에서는 AWS MCP Servers가 어떤 것인지, 어떤 역할을 하는지, 그리고 실제로 어떻게 사용하는지까지 전부 정리했습니다.
```

### MCP란?? 
> **MCP (Model Context Protocol)**는 대형 언어 모델(LLM, 예: ChatGPT)과 `외부 데이터 소스`를 `연결`해주는 오픈 프로토콜입니다.

```
즉, AI가 단순한 대화 도구를 넘어서 코드 작성, AWS 리소스 관리, 문서 검색, 비용분석 등을 실시간으로 할 수 있도록 만들어주는 연결고리이다.

이 프로토콜을 통해 AI는 AWS의 기능과 데이터를 실시간으로 받아들이고, 적절한 작업을 수행할 수 있습니다.
이 MCP를 활용하는 다양한 서버가 바로 AWS MCP Servers입니다.
```

### AWS MCP Servers 주요 구성 살펴 보기
> MCP는 하나가 있는게 아니라 여러개가 존재하는데, 
각 MCP 서버는 특정 역할을 맡고 있습니다. 아래는 주요 서버들의 기능 요약을 살펴보자

1. 🧠 Core MCP Server
> 다른 MCP 서버들을 관리하고 조율하는 중심 서버입니다.

- 여러 MCP 서버를 통합 관리
- 설정 중앙화
- 설치 및 실행 플로우 지원

예: 여러 서버가 있을 때, 이 서버가 일종의 지휘자 역할을 합니다.


2. 📘 AWS Documentation MCP Server
> AWS 공식 문서와 베스트 프랙티스를 검색하고 AI가 활용할 수 있게 해줍니다.

- 공식 문서 검색 API 기반
- 추천 문서 제공
- 마크다운 변환 지원

예: "Amazon Bedrock에 대한 예제 코드 보여줘" 라고 AI에게 질문하면, 이 서버가 실제 문서를 검색해 응답하게 합니다.

3. 🔍 Amazon Bedrock Knowledge Bases Retrieval MCP Server
> Amazon Bedrock 지식 베이스에 접근해 자연어로 질의하고, 데이터를 검색할 수 있습니다.

- 지식 베이스 및 데이터 소스 조회
- 자연어 검색
- 필터링 및 결과 재정렬

4. 🏗 AWS CDK MCP Server
> AWS CDK 기반의 인프라 자동화를 지원하는 서버입니다.

- CDK 프로젝트 분석
- 인프라 설계 추천
- 최신 AWS CDK 베스트 프랙티스 제공

예: "S3와 Lambda를 연결하는 CDK 코드 만들어줘" 요청 시 AI가 실전 코드 작성 가능.

5. 💰 Cost Analysis MCP Server
> AWS 사용 비용을 분석하고 시각화해주는 서버입니다.

- 자연어 질의 기반 비용 분석
- 리포트 생성
- 인사이트 제공

6. 🎨 Amazon Nova Canvas MCP Server
> 텍스트 기반 이미지 생성을 AWS 인증 환경에서 처리할 수 있게 해주는 서버입니다.

- 색상 기반 이미지 생성
- 생성 결과 워크스페이스 저장
- AWS 프로필 인증 기반

예: UI 설계 도중 필요한 프로토타입 이미지를 즉석에서 생성 가능.

### ⚙️ 설치 및 설정 방법
> 각 서버는 독립적으로 설치할 수 있으며, 기본적으로 Python과 uv를 사용합니다. 아래는 전반적인 설치 흐름입니다.

1. uv 설치
2. curl -sSf https://astral.sh/uv/install.sh | sh
3. Python 설치
4. uv python install 3.10
5. AWS 자격 증명 구성
6. aws configure
7. 서버 구성 파일 예시 (~/.aws/amazonq/mcp.json)

```
{ "mcpServers": { "awslabs.core-mcp-server": { "command": "uvx", "args": ["awslabs.core-mcp-server@latest"], "env": { "FASTMCP_LOG_LEVEL": "ERROR", "MCP_SETTINGS_PATH": "path to your mcp settings file" } }, "awslabs.cdk-mcp-server": { "command": "uvx", "args": ["awslabs.cdk-mcp-server@latest"], "env": { "FASTMCP_LOG_LEVEL": "ERROR" } } } }
```

> 각 서버별 세부 설정은 공식 문서를 참고하면 됩니다.

### 🧪 실제 활용 예시
“AI 어시스턴트가 AWS CDK로 인프라를 만들어주는 작업, 진짜 가능해?”
-> 가능합니다. 예를 들어 AWS CDK MCP Server와 연결된 AI에게 이렇게 요청할 수 있습니다.

“Amazon S3 버킷과 연결된 Lambda 함수를 생성해줘. CDK 코드로.”
-> 그러면 AI는 최신 CDK 버전과 AWS 베스트 프랙티스를 기반으로, 아래와 같은 코드를 생성해줄 수 있습니다.

```
const bucket = new s3.Bucket(this, 'MyBucket');
const fn = new lambda.Function(this, 'MyFunction', {
  runtime: lambda.Runtime.NODEJS_18_X,
  handler: 'index.handler',
  code: lambda.Code.fromAsset('lambda'),
  environment: {
    BUCKET_NAME: bucket.bucketName,
  },
});
bucket.grantReadWrite(fn);
```   
    
### ✅ 정리 및 기대 효과
> AWS MCP Servers는 AI 기반 클라우드 개발을 한층 더 똑똑하게 만들어주는 혁신적인 인프라입니다.

기존에는 사람이 일일이 문서를 찾고, 코드를 작성하고, 비용을 분석했다면이제는 AI에게 자연어로 지시하면 실시간으로 결과를 받아볼 수 있는 시대입니다.

### 기대 효과 요약:

- AI와 AWS의 자연스러운 연결
- 인프라 자동화 및 개발 생산성 향상
- 문서 검색, 비용 분석, 리소스 생성 자동화
- 복잡한 클라우드 관리의 단순화


#### 📌 이제 AWS도 AI와 함께 똑똑하게 관리하세요.
> 복잡함은 AI에게 맡기고, 당신은 더 중요한 개발에 집중해보는 건 어떨까요?

##### 출처 | https://digitalbourgeois.tistory.com/990#google_vignette [평범한 직장인이 사는 세상:티스토리]

---

### 자 그렇다면 여기서 궁금점
```
아! 그러면 MCP는 AWS서비스를 사용하는데 있어 각 서비스에서의 LLM과의 연결을 좀 더 쉽게 잘 연결해주기 위한 도구구나? 라고 
생각이 되는데 그러면 이게 AmazonQ인건지?
또한 그렇다면 AWS 서비스마다 따로 Lambda나 이런곳에 따로 코드를 작성해서 사용해야하는건지 등이 궁금함
```

### [답변]

1. MCP != Amazon Q
    - AmazonQ는 LLM인거고 이것과 AWS서비스를 사용하는데 도움을 주는 Tool(도구)가 MCP이다.
        - 그렇다면 또 질문 그러면 AmazonQ는 Aws에서 제공하는건데 모든 MCP가 내재되어있는건지
        아니면 또 따로 MCP를 활용해야하는건지
            - 답변 : 아니다. 구체적인 도구 활용해야함

2. MCP 서버를 사용할 때 Lambda가 필수인가?
3. MCP 서버 설정은 어디서 하는가? (백엔드? 프론트? AWS 콘솔?)
4. Amazon Q에서 MCP 서버를 붙여서 AWS 콘솔 제어는 어떻게 되는가?
-> 결론 : MCP 서버를 사용하기 위해 Lambda는 필수가 아니다.MCP 서버는 어디서든 동작할 수 있으며,Amazon Q 설정 파일에서 MCP 서버를 등록하면 된다.

| 그리고 Amazon Q는 AWS 콘솔 내부용 AI 도우미가 아니라,로컬 개발 환경에서도 사용할 수 있는 “LLM 개발 파트너 툴”이다.(물론 AWS 콘솔과 통합도 가능하지만 “AWS 안에서만” 쓰는 것은 아님)

