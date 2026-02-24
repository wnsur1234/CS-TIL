# MySQL에서 InnoDB에 대하여

* [출처](https://velog.io/@inhwa1025/MySQL-InnoDB%EB%9E%80)

---

> InnoDB는 MySQL을 위한 데이터베이스 엔진이다.

### 데이터베이스 엔진이란?
- 데이터베이스 엔진 (또는 `스토리지 엔진`)은 DBMS가 데이터베이스에 대해<br>
<u>데이터를 삽입, 추출, 업데이트 및 삭제(CRUD 참조)하는 데 사용</u>하는 기본 소프트웨어 컴포넌트

- 데이터베이스 엔진을 조작할 때 DBMS `고유의 사용자 인터페이스를 이용하는 방법`과 `포트 번호를 이용`하는 방법이 있음
- 대부분 DBMS는 고유의 `사용자 인터페이스를 통하지 않고`, 사용자가 `내장된 엔진과 상호작용 할 수 있는 자신만의 API`를 포함

[보다자세한내용](./데이터베이스_엔진.md)

### InnoDB
MySQL 5.5 버전 이후 기본적으로 MyISAM 대신 InnoDB 사용
MySQL 바이너리에 내장되어 있음
트랜잭션 지원
동시성 제어 가능
데이터 무결성 보장
제약조건, 외래 키 지원
MVCC(Multi Versioning Concurrency Control) 구조로 동작
Row-level Lock (행 단위 Lock) 사용으로 변경 작업(INSERT, UPDATE, DELETE)의 속도가 빠름
MVCC에 따른 InnoDB 특성
read 수행 시 write 중이어도 Block되지 않음 (read와 read도 서로 Block되지 않음)
read 내용은 격리 수준에 따라 내용이 바뀔 수 있음
write 시 배타적 Lock을 얻음
기본적으로 행 단위로 Lock되며 트랜잭션 종료까지 유지됨
격리 수준이나 InnoDB 설정에 따라 실제 Lock되는 행의 범위가 다를 수 있음
write와 write의 경우 나중에 온 트랜잭션이 Lock을 획득하려고 할 때 Block 됨
일정 시간 사이에 Lock을 획득하지 못하면 Lock Timeout
write 시 이전의 데이터를 UNDO 로그로 Rollback Segment 영역에 유지
UNDO 로그의 용도
write 트랜잭션 rollback 시 write 전으로 되돌리기 위해 이용
복수의 트랜잭션으로부터 격리 수준에 따른 write 데이터 참조에 이용
같은 행을 write 할 때마다 UNDO 로그가 작성되어 같은 행에 대한 복수 버전 존재
InnoDB 사용 시 주의 사항
테이블 컬럼 수는 최대 1000개
Key의 최대 길이는 3500byte이지만, MySQL에서 1024byte로 제한함
LongBlob, LongText 컬럼은 최대 4GB