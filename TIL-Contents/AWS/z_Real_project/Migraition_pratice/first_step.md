# 데이터베이스 마이그레이션 실습

## 마이그레이션
> 온프레미스 환경에서 AWS서비스와 연결 하는 것

### DB를 마이그레이션할 떄
> 중점적 객념 존재

- VPN
    - 인터넷기반 + 암호화된 전용 통신
    - Direct Connect보단 저렴
- Transit GateWay
    - AWS 내 여러 네트워크들을 허브 중심으로 연결해주는 네트워크 라우팅 허브
    - 즉, `네트워크 교통센터`/ `라우팅의 중앙 허브`

### TransitGateWay의 필요성
> VPC가 여러 개 생기면 서로 연결해야하는데,

- VPC <-> VPC
- 온프레미스 ↔ VPC
- 여러 계정에 있는 VPC끼리 연결
- VPN 연결도 여러 개
- Direct Connect도 있음

이렇게 될수록 네트워크가 거미줄처럼 복잡해집니다.

Transit Gateway는 이 문제를 푸는 핵심 서비스로, 모든 네트워크의 중심에 놓입니다.

### APIGateWay와의 차이점

- APIGateWay는 api요청을 Http 기반 레벨의 요청 처리
- TransitGateWay는 네트워크 연결

> 즉, 완전히 다른 레이어