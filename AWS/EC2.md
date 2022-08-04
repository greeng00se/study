> Stephane Maarek님의 AWS Certified Developer Associate 시험 합격의 모든 것을 수강하고 정리한 글입니다.
> 

### EC2(Elastic Compute Cloud)

- Infrastructure as a Service
- AWS에서 제공하는 클라우드 컴퓨팅 서비스

### Instances Types(인스턴스 유형)

- m5.2xlarge
    - m → instance class
    - 5 → generation
    - 2xlarge → size within the instance class
- (M) General Purpose → 범용 인스턴스
    - ex) t2.micro
- (C) Compute Optimized → 컴퓨팅 최적화 인스턴스
    - 다음과 같은 작업에 유리
        - Batch processing workloads
        - Media transcoding
        - High Performance web server
- (R) Memory Optimized → 메모리 최적화 인스턴스
    - 다음과 같은 작업에 유리
        - 고성능 관계형 데이터베이스
        - key-value 유형의 메모리 캐싱을 제공하는 캐시 저장소
        - HPC, EDA 애플리케이션
- (D) Storage Optimized → 스토리지 최적화 인스턴스

### 보안 그룹

- 인바운드, 아웃바운드 트래픽을 제어한다.
- 인스턴스 밖의 방화벽이라고 생각하면 될 것 같다.
- 여러 인스턴스에 하나의 보안 그룹을 연결할 수 있고
- 하나의 인스턴스에 여러 개의 보안 스룹을 연결할 수 있다.
- ssh 접근을 위해 별도의 보안 그룹을 만드는 것이 좋다.

### Boostrapping

- 사용자 데이터 편집에서 가능
- launching commands when a machine starts
- such as
    - installing updates
    - installing software

### 인스턴스 구입 옵션

- On-Demand Instance → 온디맨드 인스턴스
    - 인스턴스에 대한 비용을 초 단위로 지불한다.
- Savings Plans
    - 1년 또는 3년
    - 시간당 비용으로 일관된 사용량을 약정하여 비용 절감 할 수 있다.
    - 인스턴스 유형이 아닌 비용으로 약정한다.
- Reserved → 예약 인스턴스
    - 1년 또는 3년, 약 70% 정도 절약할 수 있음
    - 인스턴스 유형 및 리전을 포함하여 일관된 인스턴스 구성을 약정
    - 시간에 따라 인스턴스 유형을 변경하는 경우 사용하면 좋다.
    - 안정적인 서비스가 필요한 경우 사용하는 것이 좋다.
- Spot Instances → 스팟 인스턴스
    - 미사용 EC2 인스턴스를 요청
    - 가격이 가장 저렴하며, 더 높은 가격을 지불하는 사용자가 나타나면 인스턴스가 중단된다.
    - 데이터 분석, 배치 작업, 백그라운드 프로세싱과 같은 작업에 유리하다.
- Dedicated Hosts → 전용 호스트
    - 물리적 서버를 예약하고, 인스턴스 배치도 제어할 수 있다.
    - 인스턴스 실행을 전담하는 실제 호스트 비용을 지불하며, 기존의 소켓, 코어 또는 VM 소프트웨어별 라이선스를 가져와 비용을 절감한다.
    - 규정 준수 요구사항이 있거나 서버 결합 소프트웨어 라이센스를 사용하는 경우.
- Dedicated Instances → 전용 인스턴스
    - 인스턴스 배치를 제어할 순 없다.
    - 단일 테넌트 하드웨어에서 실행되는 인스턴스 비용을 시간 단위로 지불합니다.
- Capacity → 용량 예약
    - 언제든 접근할 수 있는 인스턴스 용량을 예약한다.
    - 원하는 기간 동안 특정 가용 영역의 EC2 인스턴스에 대해 용량을 예약합니다.
    - 실행되지 않아도 용량은 부과된다.