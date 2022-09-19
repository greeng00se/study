### 개요
- Elastic Beanstalk에서 데이터베이스를 생성하고 분리하여 사용해보겠습니다.
- RDS에서 생성하지 않고 이렇게 Beanstalk 환경에서 생성하고 분리한다면 따로 Beanstalk에서 환경변수를 설정하지 않아도 됩니다.
- 사용할 수 있는 환경변수는 다음과 같습니다.

<img width="1080" alt="image" src="https://user-images.githubusercontent.com/58586537/191059768-1ff1dbc6-60db-4400-9b2c-d948024f69cf.png">

### 데이터베이스 생성

<img width="1251" alt="image" src="https://user-images.githubusercontent.com/58586537/191058358-529b9524-46d5-4760-9542-c65ea9bd24da.png">

- 구성 → 데이터베이스 → 편집 버튼을 눌러 데이터베이스를 생성합니다.
- 향후 데이터베이스를 분리하여 사용하기 위해 유지 정책으로 생성합니다.

### 데이터베이스 분리

<img width="965" alt="image" src="https://user-images.githubusercontent.com/58586537/191057920-07cd971f-5902-4ec2-9bb1-a914308fe1aa.png">

- 스크린샷을 찍지 못해 AWS의 이미지를 사용하였습니다.
- 구성 → 데이터베이스 → 편집 버튼을 눌러 데이터베이스를 분리합니다.
- Elastic Beanstalk 환경과 따로 관리하기 위해 분리합니다.

### Spring boot 설정

- Gradle dependencies에 `runtimeOnly 'mysql:mysql-connector-java'`를 추가합니다.
- application.yml 파일에 다음과 같이 설정합니다.

```yaml
spring:

  datasource:
    url: jdbc:mysql://${RDS_HOSTNAME}:${RDS_PORT}/${RDS_DB_NAME}
    username: ${RDS_USERNAME}
    password: ${RDS_PASSWORD}
    driver-class-name: com.mysql.cj.jdbc.Driver
```

### 참고자료
- https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/java-rds.html
- https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/using-features.managing.db.html#using-features.decoupling.db