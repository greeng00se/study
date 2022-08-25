# EBS

### EBS(Elastic Block Store)

- 인스턴스가 실행할 동안 사용가능한 네트워크 드라이브
- 인스턴스가 종료되어도 데이터를 영속화할 수 있다.
- CCP 레벨 인스턴스는 한 번의 하나의 인스턴스에만 마운트할 수 있다.
- 특정 가용 영역에만 사용할 수 있다.
    - 서울 리전에서 생성된 경우 다른 리전에서 사용할 수 없다.
- 최대 IOPS에 대한 최소 크기 → 5334GB의 16,000IOPS (2022년 기준)

### EBS Volume

- 네트워크 드라이브기 때문에 마운트, 언마운트가 편리하다.
- Locked to an AZ, 스냅샷 이용하여 다른 AZ에서도 사용 가능
- EC2 인스턴스를 사용하는 경우 종료시 삭제 기능을 통해 EC2 인스턴스가 종료될 때 같이 삭제 가능

### EBS Snapshots

- 복구, 다른 리전으로 데이터를 이전시키기 위해 사용
- EBS Snapshot Archive 이용하면 75% 싼 가격으로 이용가능
    - 가격이 싸지만 아카이브 스냅샷을 이용하면 복구에 24시간 ~ 72시간 정도가 걸린다.
- Recycle Bin for EBS Snapshots
    - 스냅샷에 대한 휴지통 기능을 사용할 수 있음

### EBS 볼륨 종류

- gp2, gp3 → 범용 SSD 볼륨
- io1, io2 → 고성능 SSD 볼륨
- st1 → 저비용 HDD 볼륨
- sc1 → 초저비용 HDD 볼륨

### EBS 다중 연결

- 다중 연결을 사용하면 하나의 EBS 볼륨을 동일한 AZ의 여러 EC2 인스턴스에 연결할 수 있다.

### AMI(Amazon Machine Image)

- 소프트웨어 구성이 기재된 템플릿이다. AWS의 도커 이미지와 같은 느낌이다.
- PUBLIC AMI와 커스텀 AMI가 존재한다.
- AMI는 특정 AWS 지역용으로 구축되며 각 AWS 지역마다 고유하다.

### AMI Process

1. EC2 인스턴스를 커스텀한다.
2. 인스턴스를 종료시킨다.
3. AMI를 빌드한다.

### Instance Store

- 인스턴스의 생명주기 동안에만 관리되는 스토어다.
- IO에서 좋은 성능이 필요한 경우 고려할 수 있다.

### 참고 자료

- [https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ebs-volume-types.html](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ebs-volume-types.html)