> Stephane Maarek님의 AWS Certified Developer Associate 시험 합격의 모든 것을 수강하고 정리한 글입니다.
> 

### IAM(Identity and Access Management)

- AWS 리소스에 대한 액세스를 안전하게 제어할 수 있는 서비스다.

### IAM 기능

- AWS 계정에 대한 공유 액세스
- 세분화된 권한 관리
- 계정과 개별 사용자에게 MFA 추가가능
- 자격 증명 보고서 확인가능

### IAM 정책

- 정책이란 자격 증명이나 리소스와 연결될 때 해당 권한을 정의하는 AWS의 객체이다.
- IAM 자격 증명(사용자, 사용자 그룹 또는 역할) 또는 AWS 리소스에 연결하여 AWS에서 액세스를 관리한다.

```json
{
  "Version": "2012-10-17",
  "Id": "S3-Account-Permissions",
  "Statement": [
    {
      "Sid": "1",
      "Effect": "Allow",
      "Principal": {
        "AWS": ["arn:aws:iam::12346132421:root"]
      },
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": ["arn:aws:s3:::mybucket/*"]
    }
  ]
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

### AWS Access

- AWS Management Console: password + MFA
- AWS Command Line Interface: protected by access keys
- AWS Software Developer Kit (SDK): for code, protected by access keys

### IAM Roles for Services(역할)

- 서비스에 역할을 부여하여 권한을 줄 수 있다.

### IAM Security Tools

- IAM Credentials Report(account-level) → 자격 증명 보고서
- IAM Access Advisor(user-level) → 액세스 관리자

### IAM Guidelines & Best Practices

- Root 계정을 사용하지 말아야 한다. 또한 MFA를 설정해야 한다.
- One physical user = One aws user
- 그룹을 이용하면 그룹 수준에서 권한을 제어할 수 있다.
    - 그룹은 사용자만 포함시킬 수 있다.
- 강력한 패스워드 정책을 설정해야 한다.
- MFA를 사용하는 것이 좋다.
- Roles를 이용하여 서비스에 대한 권한을 줄 수 있다.
- CLI나 SDK를 사용할 땐 액세스 키를 만들어야 한다. 절대 공유 금지
- 계정의 권한을 감사할 때는 IAM 자격증명 보고서와 액세스 관리자를 사용할 수 있다.
- 절대 IAM user & Access Keys를 공유하면 안된다.

### 정리

- 사용자: 실제 사용자와 IAM 사용자가 매핑되어야 한다.
- 사용자 그룹: User만 포함시킬 수 있다. 그룹은 그룹을 포함시킬 수 없음.
- 정책: JSON 형식의 문서로 사용자나 그룹이 할 수 있는 권한을 알려주는 문서이다.
- 역할: AWS 서비스를 위한 권한이다.
- 보안: MFA + 강한 패스워드 정책
- 액세스 키: AWS CLI, AWS SDK를 위한 키값으로 비밀 키는 공유해서는 안된다.
- 감사: 자극 증명 보고서 → 사용자 관련 정보 확인, 액세스 관리자 → 사용자의 최근 권한 사용 내역 확인