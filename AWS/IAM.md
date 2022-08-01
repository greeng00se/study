### IAM(Identity and Access Management)

- AWS 리소스에 대한 액세스를 안전하게 제어할 수 있는 서비스다.

### IAM 기능

- AWS 계정에 대한 공유 액세스
- 세분화된 권한 관리

### IAM 정책

### IAM 정책 구조

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "dynamodb:*",
    "Resource": "arn:aws:dynamodb:us-east-2:123456789012:table/Books"
  }
}
```

- Version: 정책 버전
- Id: 정책을 식별하는 ID(Optional)
- Statement: 정책
    - Sid: Statement ID
    - Effect: 접근 허용 여부
    - Principal: 특정 정책이 적용될 계정, 역할
    - Action: API 호출 목록
    - Resource: Action의 리소스 목록