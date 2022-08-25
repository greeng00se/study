# ELB

### ELB(Elastic Load Balancer)

- AWS의 관리해주는 로드 밸런서 서비스
- AWS가 제공하는 로드밸런서에는 4가지 종류가 있다.

### Health Check

- Protocol: HTTP
- Port: 4567
- Endpoint: /health

### Classic Load Balancer - 2009 - CLB

- HTTP, HTTPS, TCP, SSL → Layer 4, Layer 7

### Application Load Balancer - 2016 - ALB

- HTTP, HTTPS, WebSocket → Layer 7
- URL 기반, Query String & Parameter기반 라우팅도 가능
- Application LB의 경우 클라이언트의 IP가 X-Forwarded-For 헤더에 저장된다.
    - 인스턴스에 접근할 땐 로드밸런서의 프라이빗 IP 사용

### Network Load Balancer - 2017 - NLB

- TCP, UDP → Layer 4
- 낮은 지연시간 ~ 100ms (vs 400 ms for ALB)
- AZ당 고정 IP 하나를 지원한다.
- 다음과 같은 대상 그룹을 가질 수 있다.
    - EC2 Instance
    - IP Address
    - ALB (고정 IP를 가질 수 있도록, 규칙은 ALB의 룰을 사용)

### Gateway Load Balancer - 2020 - GWLB

- Operates at Layer 3 - IP Protocol
- 타사 가상 어플라이언스의 가용성을 쉽고 비용 효율적으로 배포, 확장 및 관리 할 수 있는 서비스
    - 방화벽 침입 감지 및 탐지 시스템 클라우드의 심층 패킷 검사 시스템을 사용할 수 있도록 한다.
- 클라이언트 → GWLB → F/W or IDS/IPS → GWLB → App
- 기능
    - 투명 네트워크 게이트웨이(모든 트래픽이 오가는 단일 지점)
    - LB
- UDP 6081 포트 GENEVE(Generic Network Virtualization Encapsulation) 프로토콜 사용
    - GENEVE를 사용하여 모든 IP 패킷을 수신하고 리스너 규칙에 지정된 대상 그룹에 트래픽을 전달
    - GENEVE는 UDP를 통해 구현된 캡슐화 프로토콜
- 다음과 같은 대상 그룹을 가질 수 있다.
    - EC2 instances
    - IP Address

### Sticky Sessions

- 로드밸런서가 특정 세션의 요청을 처음 처리한 서버로만 전송하는 것
- ALB, CLB에서 설정 가능
    - 대상 그룹의 → Edit attributes → Stickiness 통해서 설정 가능하다.
- 쿠키를 사용하여 기능을 지원한다.
    - Application Based Cookies
        - 사용자 정의 쿠키
        - 애플리케이션 쿠키
            - AWSALBAPP 쿠키 사용
    - Duration-based Cookies(기간 기반 고정 쿠키)
        - AWSALB for ALB, AWSELB for CLB 쿠키 사용
- 사용 → 세션 데이터를 잃지 않기 위하여 사용된다.
- 단점 → 일부 인스턴스에 사용자가 몰리면서 부하에 불균형이 생길 수 있다.

### Cross-Zone Load Balancing(교차 영역 로드 밸런싱)

- 해당 옵션은 AZ의 인스턴스 수에 상관없이 요청을 골고루 분산 시킨다.
    - 예) AZ-1 2대 AZ-2 8대의 인스턴스가 존재하고 총 1000개의 요청이 있다고 가정
    - Enable → 인스턴스별로 요청을 분산시킨다.
        - 모든 인스턴스는 100개의 요청을 받는다.
    - Disable → AZ별로 요청을 분산시킨다.
        - AZ-1에 존재하는 인스턴스는 각각 250개의 요청을 받는다.
        - AZ-2에 존재하는 인스턴스는 각각 62개 정도의 요청을 받는다.
- LB별 기본 옵션
    - ALB → Always on
    - NLB → Disabled by default
        - 교차 영역 로드 밸런싱을 활성화하려면 비용 지불을 해야한다.
    - CLB → Disabled by default

### SSL/TLS

- 클라이언트와 LB간의 통신을 암호화하기 위해 사용
- AWS ACM을 통해서 인증서를 관리할 수 있다.
- CLB → 하나의 인증서만 허용
- ALB → SNI를 통해 여러개의 인증서 허용
- NLB → SNI를 통해 여러개의 인증서를 허용
- SNI(Server Name Indication)
    - 웹 서버상의 다수의 SSL 인증서를 발급해 단일 웹 서버가 여러 개의 웹 사이트를 제공하도록 한다.

### ASG(Auto Scaling Group)

- Scale out, Scale in에 대한 정책 설정, 최소 인스턴스 최대 인스턴스를 설정하고 LB와 연동하여 사용
- CloudWatch의 알람을 기반으로 Scale out, in 정책을 설정할 수 있다.
- 커스텀 메트릭을 통해서 다양한 지표를 이용하여 Auto Scaling도 가능하다.

### ASG Dynamic Scaling

- Target Tracking Scaling
    - CPU 사용률 기준으로 설정할 수 있다.
- Simple / Step Scaling
    - CloudWatch alarm을 이용하여 설정할 수 있다.
    - 단계적으로 Scale out, Scale in 할 수 있다.
- Scheduled Actions
    - 사용 패턴을 바탕으로 스케일링을 할 수 있다.
    - 큰 이벤트가 있을 때 시간에 맞춰 scale out을 할 수 있다.
    

### ASG Predictive Scaling

- 과거 로드를 분석하여 예측을 기반으로 스케일링 작업을 예약한다.
- 머신러닝 기반으로 동작

### ASG Good metrics

- CPU
- Request count per target
- Average network In & Out
- Any custom metric

### ASG Scaling Cooldowns

- 스케일링이 진행된 후 휴지기간을 가진다.
- 기본값은 300초다.

### 참고 자료

- [https://www.udemy.com/course/best-aws-certified-developer-associate/](https://www.udemy.com/course/best-aws-certified-developer-associate/)
- [https://www.rfc-editor.org/rfc/rfc8926](https://www.rfc-editor.org/rfc/rfc8926)
- [https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html)