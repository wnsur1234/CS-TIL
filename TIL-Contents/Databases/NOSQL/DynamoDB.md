# DynamoDB 

### DynamoDB를 실제로 사용하려고 하니...

``` 
DynamoDB를 실제로 사용하려고하다보니까 
막상 pk는 뭘로 둬야하는거고 sortKey 이런건 어떻게
설계를 해야할지 도저히 감히 잡히질 않는다.
```

# !!!!근데 진짜 개중요!!!!
> 알고보니 Developing_2에 보다 상세하게 나와있었음!!!

바로 가고 싶다면? [참고](../../AWS/developing/aws_developing_2.md) 
여기서 `DynamoDB를 검색해서 쭉 읽어 볼것`

### 나의 요구사항
나는 AIagnet에 정보를 전달하기 위해
1. 프롬프트를 보내야하고 
2. 응답값이 Json형식으로 돌아오는데 이 값을 저장해야한다.

`그렇다면`??

이 두개만 저장하면 되는건지
추가로 저장을 한다면 어떠한 정보를 저장하는게 좋을까??

계획 1, 계획 2, 계획 3... 이렇게 나올꺼고
`[하나의 흐름에서의 추가 프롬프트 질문, 새로운 채팅창같이 새로운 프롬프트 입력]` 들이
나올건데 이런건 어떠한 형식으로 저장되는지 새로운 컬럼으로 생성되어 저장되는건지 등을 
알아보아야겠다.

### DynamoDB 강좌 찾아보기

### 구성요소
- 키
    - PK(Partition key)
        - 파티션을 식별
        - 얘는 항상 `==, <, >, >=, <=` 연산자만 사용가능
    - Sort Key(SK)
        - 동일한 파티션 내에서 항목을 정렬
        - 근데 얘는 굳이 사용하지 않아도 된다고 함 
        - <u>`1:N 관계 모델링 할 수 있음`</u>
        - 얘는 "begins with", "between", 등호연산자를 사용 가능

- 보조인덱스
    - GSI(Global Secondary Index)
        - 전체 데이터를 대상으로
        - 특정 컬럼을 검색하고자 할 때 사용.
        - 얘는 새로운 복합 키를 생성하는것과 같다
        
    - LSI(Local Secondary Index)
        - 동일 파티션 키 내에서 작동        
        - 인덱스와 같다

### Dynamo에서 제공하는 데이터 조회 방법

- 쿼리(Query)
    - 특정 값에 일치하는 항목만 검색합니다.

- 스캔(Scan) : 비용이 많이 듬
    - 테이블의 모든 항목을 검색합니다.

- 여기서 primaryKey의 중요성이 나오는데
  오직 primaryKey(partition key, sort key)로 만 쿼리가 가능하다.

---

```
위에까지 내용은 공통적으로 딱 저기까지만 정보를 제공하는거 같다.
```

### 테이블 구조

- DynamoDB의 가장 큰 구조는 테이블이다.
    - 테이블에는 무한대에 가까운 아이템을 입력할 수 있다.
    - 아이템과 attribute

- attribute = cloum과 같은 개념으로
    - attribute가 모이면 attributes가 된다.
```
ex)
{
  "userId": "U123",  -> 하나하나가 attribute
  "movieId": "M455",
  "rating": 4,
  "createdAt": "2025-12-08"
}
전체가 attributes
```

- 아이템(Row,행) -> 행거
    - attributes가 모여서 아이템이된다.

![alt text](../../a_images/item&attribute_DynamoDB.png)


![alt text](../../a_images/Dynamo_structure.png)