# Elastic Beanstalk

## Elastic BeanStalk

- 웹 어플리케이션을 배포하고, 확장하고 관리하는데 있어 쉽고 빠르게 할 수 있도록 도와주는 완전 관리형 서비스(Managed Service)입니다.
- 기본적으로 nginx의 리버스 프록시를 사용하여 80 → 5000번 포트에 매핑합니다.
- nginx 구성을 확장하거나 커스텀할 수 있습니다.

## Beanstalk Deployment Options

- All at once
- Rolling
- Rolling with additional batches
- Immutable

### All at once

- 모든 인스턴스를 동시에 종료시키고, 동시에 버전업을 실행
- 빠르다.
- 애플리케이션이 종료되는 시점이 있다.(downtime)

### Rolling

- 버킷 사이즈를 설정해야한다.(한 번에 버전 업할 인스턴스 수)
- 돌아가면서 인스턴스를 종료시키고, 버전업을 실행한다.
- 배포 시간이 길어질 수 있다.(인스턴스가 매우 많은 경우)
    - 버킷 사이즈 1개, 인스턴스 100개 인 경우….

### Rolling with additional batches

- Beanstalk최대 용량에서 실행된다.
- 프로덕션에서 좋은 방법이다.
- 추가 인스턴스를 생성하여 동작한다 + Rolling
- 현재 인스턴스가 4개고 버킷 사이즈가 2개라면 인스턴스를 추가로 2개 실행해서 버전 업을 한 후 그 다음에 롤링과 같이 동작하고 버전업이 끝난다면 배포를 위해 추가로 생성한 인스턴스를 종료한다.

### Immutable

- Zero Downtime
- 새 코드를 새로운 인스턴스에 배포한다.
- 비용이 많이 든다.
- 새로운 ASG(Auto Scaling Group)을 만들어서 버전업을 하고 기존 ASG와 병합한 후 기존 ASG에 있는 구버전 인스턴스는 제거한다.

### Blue / Green

- Zero Downtime
- 새로운 환경을 생성해서 배포한다.
- 배포가 끝난다면 swap URLs를 통해 환경을 변경한다.
- 예) 블루(기존 환경)에 90%의 트래픽를 보내고, 그린(새로운 배포환경에) 10%의 트래픽을 보낸다. 정상적으로 동작한다면 그린으로 swap URL
- 장점
    - 이전 환경이 실행중임으로 swap URL로 빠른 롤백을 할 수 있다.
- 단점
    - 새로운 환경을 생성하기 때문에 느리다.

### Elastic Beanstalk - Traffic Splitting

- Use for Canary Testing
- 같은 용량의 ASG에 새로운 애플리케이션이 배포되고, 설정하는 동안 Temporary ASG로 소량의 트래픽이 간다.
- temp ASG가 안정적이고 문제가 없다면 original ASG로 이전한다.