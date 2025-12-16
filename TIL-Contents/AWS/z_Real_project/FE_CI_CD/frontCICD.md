#

---

아주 좋습니다 👍
그럼 오르미캠프 프로젝트 기준으로
👉 “프론트 CI/CD를 처음부터 끝까지, 클릭 위치까지 포함해서”
**정확한 순서표(체크리스트)**로 정리해드리겠습니다.

목표
- GitHub에 push → 자동으로 S3 배포 → CloudFront 반영

## 🧭 전체 단계 한 줄 요약
### AWS (S3, CloudFront, IAM)
 - GitHub (OIDC 설정)
 - GitHub Actions (YAML 작성)
 - Frontend 코드 환경변수 설정
 - 배포 테스트

## 1️⃣ AWS 콘솔에서 할 일 (먼저)
#### ✅ 1-1. S3 버킷 생성

위치: AWS Console → S3

버킷 이름: jeju-ormi-frontend-prod

리전: ap-northeast-2

퍼블릭 액세스: ❌ (모두 차단)

정적 웹 호스팅: ❌ (사용 안 함)

### 📌 이유

> CloudFront가 대신 공개합니다

### ✅ 1-2. CloudFront 생성
 
위치: AWS Console → CloudFront → Create distribution

Origin domain: 위 S3 버킷 선택

Origin access: OAC (권장)

Viewer protocol: Redirect HTTP → HTTPS

Default root object: index.html

### 📌 생성 후

Distribution ID 저장 (중요)

> CloudFront 도메인 확인

### ✅ 1-3. S3 버킷 정책 연결 (OAC)

CloudFront 생성 시 자동 or 수동

```
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": { "Service": "cloudfront.amazonaws.com" },
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::jeju-ormi-frontend-prod/*",
    "Condition": {
      "StringEquals": {
        "AWS:SourceArn": "arn:aws:cloudfront::<ACCOUNT_ID>:distribution/<DISTRIBUTION_ID>"
      }
    }
  }]
}
```

## 2️⃣ IAM 설정 (GitHub Actions용) ⭐ 핵심
### ✅ 2-1. GitHub OIDC Provider 생성

> 위치: AWS Console → IAM → Identity providers

```
Type: OpenID Connect

Provider URL:

https://token.actions.githubusercontent.com


Audience:

sts.amazonaws.com
```
✅ 2-2. IAM Role 생성

위치: IAM → Roles → Create role

Trusted entity: Web identity

Identity provider: GitHub

Audience: sts.amazonaws.com

Trust Policy (중요)
```
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "Federated": "arn:aws:iam::<ACCOUNT_ID>:oidc-provider/token.actions.githubusercontent.com"
    },
    "Action": "sts:AssumeRoleWithWebIdentity",
    "Condition": {
      "StringLike": {
        "token.actions.githubusercontent.com:sub": "repo:YOUR_GITHUB_ID/YOUR_REPO:*"
      }
    }
  }]
}
```
### ✅ 2-3. IAM Role 권한 정책

> Inline or Managed Policy

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:DeleteObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::jeju-ormi-frontend-prod",
        "arn:aws:s3:::jeju-ormi-frontend-prod/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": "cloudfront:CreateInvalidation",
      "Resource": "*"
    }
  ]
}
```

---

## 📌 Role ARN 복사해두세요

### 3️⃣ GitHub에서 할 일
### ✅ 3-1. GitHub Repository → Settings → Secrets

Secrets는 없어도 됩니다 (OIDC라서)

### 👉 중요한 건 Actions 권한

### ✅ 3-2. Actions 권한 설정

Settings → Actions → General

Workflow permissions
☑️ Read and write permissions

☑️ Allow GitHub Actions to create and approve PRs

4️⃣ GitHub Actions YAML 작성

위치:
.github/workflows/frontend-deploy.yml

```
name: Frontend Deploy

on:
  push:
    branches: [ "main" ]

permissions:
  id-token: write
  contents: read

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 18

      - run: npm ci
      - run: npm run build

      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::<ACCOUNT_ID>:role/FrontendDeployRole
          aws-region: ap-northeast-2

      - run: aws s3 sync build/ s3://jeju-ormi-frontend-prod --delete

      - run: |
          aws cloudfront create-invalidation \
            --distribution-id <DISTRIBUTION_ID> \
            --paths "/*"

```

### 5️⃣ 프론트 코드 환경변수
.env.production
VITE_API_BASE_URL=https://api.jeju-ormi.com


### 📌 build 시 주입됨

### 6️⃣ 배포 테스트 순서

main 브랜치 push

GitHub Actions 성공 확인

S3 객체 업로드 확인

CloudFront 접속

API 호출 정상 여부 확인

### 7️⃣ 자주 터지는 포인트 (중요)
문제	원인
AccessDenied	IAM Role 정책
화면 안 바뀜	CloudFront invalidation 누락
API 호출 실패	CORS / API URL
Actions 실패	role-to-assume ARN 오타
🔥 다음 단계 추천

이제 다음 중 하나로 가면 딱 좋습니다:

1️⃣ dev / prod 분리 CI/CD
2️⃣ 백엔드 EKS CI/CD 연동
3️⃣ Route53 도메인 연결
4️⃣ 아키텍처 다이어그램 (보고서용)

👉 다음은 뭐부터 같이 할까요?

---