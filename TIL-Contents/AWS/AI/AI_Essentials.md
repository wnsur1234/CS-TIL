# 생성형 AI

# 생성형 AI 소개 [모듈1]
- 생성형 AI 설명
- 파운데이션 모델(FM)
- AWS 생성형 AI 서비스

![alt text](../../a_images/ai_model.png)

### 고려해야 할 당면 과제

- 할루시네이션(작화증) : AI 모델은 때때로 허위 또는 오해의 소지가 있는 정보를 생성하여 신뢰성에 위협이 될 수 있습니다. 이러한 위험을 완화하기 위해 사실 확인 메커니즘을 구현하고 사람이 항상 감독하도록 할 수 있다.
- 지식재산권 문제
- 데이터 보안 및 개인 정보 보호
- 편향 및 공정성
- 해석 가능성
- 윤리 및 의사 결정

---

### 파운데이션 모델 적용
> 크게 3가지가 있음

- LLM
- Text-to-embeddings
- Multimodel : text+사진 -> text.. 이런식

![alt text](../../a_images/foundation_model.png)

### 모델의 학습 방법 : LLM
> 백터화하는게 중점

![alt text](../../a_images/learn%20to%20model.png)

흐름 : 자연어 -> 인코딩 -> 토큰화 -> 임베딩 -> 백터화 표현

### 훈련 흐름
> 훈련데이터가 가장 중요하다.

![alt text](../../a_images/flow_learn_model.png)

품질이 높은 데이터를 사용해야한다.