# Route 53

### DNS Terminologies

- DNS Registrar
    - Amazon Route 53, GoDaddy
- DNS Records
    - A, AAAA, CNAME, NS
- Zone File
    - contains DNS record
- Top Level Domain(TLD)
    - .com, .us, .gov
- Second Level Domain(SLD)
    - amazon.com, [google.com](http://google.com)

### How DNS Works

- Web Browser → Local DNS Server
- 그 후에 로컬 DNS 서버가 다음 순서대로 질의
    - ICANN에 등록된 Root DNS Server
    - INAN TLD Dns Server
    - SLD DNS Server

### Route 53

- 고가용성, 확장가능한, 관리형 DNS 서비스
- 달에 0.5$의 요금 부과

### Route 53 Records

- Route Traffic for a domain
- 레코드는 다음과 같은 정보를 포함한다.
    - 도메인
    - 레코드 타입
    - 값
    - 라우팅 정책
    - TTL

### Route 53 - Record Types

- A → maps a hostname to IPv4
- AAAA → maps a hostname to IPv6
- CNAME → maps a hostname to another hostname
- NS → Name Servers for the hosted zone

### Hosted Zones

- Pulbic Hosted Zones
    - 공개
- Private Hosted Zones
    - VPC 사용 비공개 URL