# VPC 실전

- VPC는 리소스 격리
    - 계정 분리 > vPC 분리
    

제일 중요한게 NAT 게이트웨이이다.

근데 Nat도 비쌈
-> 그래서 s#하는데 왜 NAt써야하냐 
해서 나온게 `엔드포인트`로 다이렉트로 접귿

Nat 안되면 보안상 또 취약해짐


NACU => 
SecurityGroup -> Allow staful

// TODO : VPC 아키텍쳐 내용 좀 더 찾아보면서 게이트웨이나 통신 하는 부분 찾아보기
