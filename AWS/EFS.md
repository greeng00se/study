# EFS

### EFS(Elastic File System)

- 여러 AZ의 여러 인스턴스가 접근 가능한 파일 시스템
- Managed NFS 네트워크 파일 시스템으로 여러 EC2 인스턴스에 마운트할 수 있다.
- 리눅스 기반 AMI에만 호환되는 파일 시스템(POSIX 파일 시스템)
- 사용하는 만큼 비용 책정(1GB 당)

### EFS Performance

- 1000개 이상의 NFS 클라이언트 동시 수용 가능
- 10GB/s
- petabyte 수준으로 오토스케일링

### Performance Mode(성능 모드)

- 범용 → 기본값으로 지연 시간이 중요한 경우에 사용한다.
- Max I/O(최대 I/O) → 빅 데이터, 미디어 처리에 적합함

### Throughput mode(처리량 모드)

- Burstring(버스팅 모드) → 파일 시스템 크기에 따라 처리량을 조정할 수 있다.
- Provisioned(프로비저닝 모드) → 처리량을 고정시킬 수 있다.

### Storage Classes(스토리지 클래스)

- Standard → 여러 AZ에 걸쳐 데이터를 중복 저장
- One Zone → 단일 AZ 내에 데이터를 중복 저장

### 수명주기 관리

- Infrequent access(EFS-IA) → X일 동안 접근 안한다면 EFS IA로 이동
- EFS-IA는 90%정도 저렴