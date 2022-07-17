## MySQL TLS 통신

- 데이터베이스와 통신을 하는 경우 기본적으로 암호화 보안 프로토콜인 SSL/TLS이 권장된다.
- SSL/TLS로 통신을 하는 경우 전달하는 데이터의 기밀성, 데이터 무결성, 인증을 제공한다.
    - 따라서 중간자 공격(MITM)으로부터 보호할 수 있다.
- MySQL은 설정을 통해 SSL 통신을 유무를 설정할 수 있고, 버전에 따라 지원하는 SSL/TLS 통신이 다르다.

![image](https://user-images.githubusercontent.com/58586537/179375374-d9c611aa-046c-4bbc-b948-3cdcc34aea5d.png)

- 만약 직접 데이터베이스를 구축하고, SSL/TLS 구성을 해야한다면 다음을 참고하여 설정을 할 수 있다.
    - [Creating SSL and RSA Certificates and Keys using MySQL](https://dev.mysql.com/doc/refman/8.0/en/creating-ssl-rsa-files-using-mysql.html)

### Database에서 지원하는 SSL/TLS 설정

- 현재 토이프로젝트에선 Azure Database for MySQL을 사용하고 있다.
- Azure Database는 2022년 기준 기본적으로 TLS 1.2 버전을 권장한다.
    - 환경 구축을 하면 기본적으로 require_secure_transport가 ON으로 설정되어 있다.
- require_secure_transport = OFF를 함으로써 SSL 통신을 안할 수도 있지만 권장하지 않는다.

### 연결에 필요한 인증서 받기

![image](https://user-images.githubusercontent.com/58586537/179375376-0715c944-a74d-447a-9623-0b315cc8e523.png)

- Azure 데이터베이스 - 네트워킹에 들어가면 SSL 인증서 다운로드가 있다.
- 버튼을 누르면 PEM 형식의 인증서 파일이 받아진다.
    - 리눅스나 맥의 경우 `keytool -printcert -file PEM파일명` 로 인증서를 자세히 확인할 수 있다.

### TypeORM 설정하기

- TypeORM 암호화 통신 설정을 하지 않고 연결하는 경우 다음의 오류가 발생한다.
    - `Error: Connections using insecure transport are prohibited while --require_secure_transport=ON.`

```tsx
export const typeORMConfig: TypeOrmModuleOptions = {
  type: dbConfig.type,
  host: process.env.DB_HOSTNAME || dbConfig.host,
  port: process.env.DB_PORT || dbConfig.port,
  username: process.env.DB_USERNAME || dbConfig.username,
  password: process.env.DB_PASSWORD || dbConfig.password,
  database: process.env.DB_NAME || dbConfig.database,
  entities: [__dirname + '/../**/*.entity.{js,ts}'],
  synchronize: dbConfig.synchronize,
  logging: dbConfig.logging,
  ssl: {
    ca: process.env.SSL_CERT,
  },
};
```

- 위와 같이 TypeORM 설정부분에 ssl.ca에 값을 PEM 파일의 경로로 설정하거나 PEM파일의 키 값이 들어있는 환경변수로 설정하면 암호화 통신을 할 수 있다.
    - [자세한 옵션은 mysqljs 참고](https://github.com/mysqljs/mysql#ssl-options)

### 환경변수 설정

- 현재 토이 프로젝트에서 dotenv를 사용하고 있다.
- dotenv는 현재 쌍따옴표로 묶여있는 [multiline variable을 지원](https://github.com/bkeepers/dotenv#multi-line-values)한다.
- 맥OS 기준 다음 명령어를 통해서 다운받은 인증서의 값을 복사할 수 있다.
    - `cat DigiCertGlobalRootCA.crt.pem | pbcopy`
- 복사한 값을 원하는 환경변수명과 함께 저장하면 된다.

```
SSL_CERT="-----BEGIN CERTIFICATE-----
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
ABCDEFGHIJKLMNOPABCDEFGHIJKLMNOPABCDEFGHIJKLMNOP
-----END CERTIFICATE-----"
```

### Github Action에서 해당 환경변수를 사용하는 경우

- 토이프로젝트에서 github PR을 요청하거나 main에 push하면 자동으로 jest 테스트가 돌아가게 되어있다.
- e2e 테스트를 하거나 통합 테스트를 하는 경우 데이터베이스와 통신이 필요하기 때문에 마찬가지로 PEM 파일에 대한 값이 필요하다.
- github action의 env를 이용해 github action이 돌아가는 docker환경에 매개변수를 세팅할 수 있다.
- secrets 값의 경우 repository secrets에 설정하면 된다.

```yaml
name: build

on:
  push:
    branches: [ "main" ]
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  sonarcloud:
    name: SonarCloud
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: Install dependencies
        run: npm ci
      - name: Test and coverage
        run: npm run test:cov
        env:
          RDS_HOSTNAME: ${{ secrets.DB_HOSTNAME }}
          RDS_USERNAME: ${{ secrets.DB_USERNAME }}
          RDS_PASSWORD: ${{ secrets.DB_PASSWORD }}
          SSL_CERT: ${{ secrets.SSL_CERT }}
          JWT_SECRET: ${{ secrets.JWT_SECRET }}
      - name: SonarCloud Scan
        uses: SonarSource/sonarcloud-github-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

## 참고 자료

- [Connect to Azure Database for MySQL, Microsoft](https://docs.microsoft.com/en-us/azure/mysql/flexible-server/how-to-connect-tls-ssl)
- [Connection options, TypeORM](https://orkhan.gitbook.io/typeorm/docs/connection-options)
- [mysql, mysqljs](https://github.com/mysqljs/mysql#ssl-options)
- [dotenv](https://github.com/bkeepers/dotenv#multi-line-values)