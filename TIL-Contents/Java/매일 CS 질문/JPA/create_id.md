# JPA에서 ID 생성 전략에 대해 설명해주세요

> 내답변

- JPA에서 ID 생성 전략중 하나의 방법으로
@ 즉, 어노테이션을 활용하는 방법이 있습니다.
- @을 활용한 ENTITY를 생성할때 무조건 @ID값을 생성해주어야하는데
이 ID값은 PK(기본키)가 되고 -> 여기에 ID의 값을 자동으로 부여하고싶다면
@GeneratedValue 어노테이션을 활용하여 튜플이 추가될때 마다 자동으로 Increase되는 값들을 저장하게됩니다.

---

> 완벽한 정답

### JPA의 ID 생성 전략은 보통 @GeneratedValue(strategy = GenerationType.XXX)로 지정하고, 대표적으로 4가지:
- ✅ GenerationType.IDENTITY
    - DB의 AUTO_INCREMENT 같은 “자동 증가 컬럼”에 맡김
    - INSERT를 해야 ID를 알 수 있음 → persist() 시점에 즉시 INSERT가 나갈 수 있어요(쓰기 지연/배치에 불리)
    - 주로: MySQL, MariaDB
- ✅ GenerationType.SEQUENCE
    - DB의 시퀀스(Sequence) 사용
    - INSERT 전에 시퀀스로 ID를 먼저 가져올 수 있음 → 성능/배치에 유리
    - @SequenceGenerator로 시퀀스 이름, allocationSize 설정 가능
    - 주로: Oracle, PostgreSQL
- ✅ GenerationType.TABLE
    - 시퀀스가 없는 DB를 위해 별도 테이블로 ID 관리
    - 동시성/성능 부담이 커서 보통 비추천
- ✅ GenerationType.AUTO
    - JPA가 DB 방언(dialect)에 따라 알아서 선택
    - 편하지만 운영에서 “어떤 전략이 되는지” 예측이 약해질 수 있음
    
> 추가로 실무에서: UUID 전략(직접 생성)이나, Snowflake 같은 분산 ID도 있지만 이는 표준 GenerationType은 아니고 커스텀으로 많이 처리합니다.

---

> 내 대답 평가 :

- 좋은 점
    - @Id가 PK라는 점 언급 ✅
    - @GeneratedValue로 자동 생성한다는 방향성 ✅
- 아쉬운 점 (면접에서 걸리는 부분)
    -  “ID 생성 전략” 질문인데 **전략 종류(IDENTITY/SEQUENCE/TABLE/AUTO)**를 거의 설명하지 않았어요. ❌
    - “튜플 추가될 때마다 자동 Increase”는 IDENTITY에 가까운 설명인데, JPA 전체 전략을 포괄하지 못해요.
    - 어떤 DB에서 어떤 전략을 쓰는지, 장단점/동작 시점(persist 시점에 ID가 필요한지 등)이 빠졌어요.

---

> 모르는 용어 정리

**persist()**
: persist()는 영속성 자체가 아니라, 엔티티를 “영속 상태(Managed)”로 만드는 메서드예요.

### persist() 한 줄 정의
- EntityManager.persist(entity)
→ 현재 엔티티를 영속성 컨텍스트에 등록해서 JPA가 관리하게 함
→ 트랜잭션 커밋 시점(또는 flush 시점)에 INSERT SQL이 DB로 나감

- 엔티티 상태로 보면
    - 비영속(new/transient): 그냥 자바 객체 (DB/JPA 관리 X)
    - 영속(managed/persistent): 영속성 컨텍스트가 관리하는 상태 (변경 감지, 1차 캐시, 쓰기 지연 등 적용)
    - 준영속(detached): 관리하다가 분리된 상태
    - 삭제(removed): 삭제 예정 상태
### persist()는 비영속 → 영속으로 바꾸는 역할입니다.
> “persist하면 바로 INSERT 돼요?” (면접 단골)

보통은:
persist() 호출 시점에 바로 DB에 INSERT가 실행되는 게 아니라
영속성 컨텍스트에 “INSERT 예약”이 걸리고
`flush/commit 때 INSERT`가 나가요.

### JPA의 “쓰기 지연”은 원래 persist 해도 INSERT를 바로 안 하고 모아두는 것인데
- IDENTITY는 ID를 얻으려면 INSERT가 필요해서
- 오히려 persist() 시점에 INSERT가 빨리 나가버려서
- 👉 쓰기 지연이 깨진다 / 적용이 어려워진다가 맞아요.

즉, “쓰기지연이 발생”이 아니라

**쓰기지연을 못 한다(덜 한다)**가 포인트입니다.

```
하지만 예외가 있어요:
IDENTITY 전략이면 persist 때 INSERT가 빨리 나갈 수 있음
ID를 DB가 만들어주니까(AUTO_INCREMENT)
JPA가 ID를 알아야 해서 INSERT를 먼저 해버리는 경우가 많아요.
그래서 “IDENTITY는 쓰기 지연(배치)에 불리”라고 말하는 겁니다.
persist vs save(스프링 데이터 JPA)
persist() : JPA 표준(EntityManager)
save() : Spring Data JPA의 편의 메서드(내부적으로 persist/merge 등을 상황에 따라 호출)
```

---

> 내일 2.07(토) 재 답변