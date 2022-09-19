### 개요

- Elastic Beanstalk은 기본적으로 nginx를 역방향 프록시 서버로 사용합니다.
- 내장 톰캣을 사용하는 Jar 파일로 배포할 때 nginx 설정하는 방법을 알아보겟습니다.

### nginx 구성 전체 재정의

```jsx
~/workspace/my-app/
|-- .platform
|   `-- nginx
|       `-- nginx.conf
`-- other source files
```

- nginx 구성 전체를 재정의 하는 경우 `.platform/nginx/` 경로 아래 `nginx.conf` 파일을 생성하고 설정 값을 넣어줍니다.

### nginx 구성 확장

```bash
~/workspace/my-app/
|-- .platform
|   `-- nginx
|       `-- conf.d
|           `-- myconf.conf
`-- other source files
```

- `.platform/nginx/conf.d/` 경로 아래 .conf로 끝나는 파일을 생성하여 nginx 설정을 넣어주면 됩니다.
- 저는 토이프로젝트에서 아래 설정을 사용하였습니다.

```bash
server {
    server_name api.myeats.me;

    location / {
        if ($request_method = 'OPTIONS') {
            add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Methods' 'GET, POST, DELETE, PATCH, OPTIONS';
            add_header 'Access-Control-Allow-Headers' 'Content-Type, Authorization';
            add_header 'Access-Control-Max-Age' 86400;
            return 204;
        }
        add_header 'Access-Control-Allow-Origin' '*' always;
        add_header 'Content-Type' 'application/json' always;
    }
}
```

### 배포할 .zip 파일 생성하기

```bash
~/application.zip
|-- .platform
|   `-- nginx
|       `-- conf.d
|           `-- myconf.conf
`-- application.jar
```

- 압축을 해제한다면 위와 같은 구성이 되도록 압축해줍니다.

```bash
# 빌드 후 jar 파일 프로젝트 최상위 경로로 이동
mv ./build/libs/application.jar .

# 압축
zip -r application.zip .platform application.jar
```

- Jar 파일과 .platform 아래 모든 파일을 같이 압축하고 EB에 배포하면 됩니다.

### 다수의 jar파일 배포 혹은 프로세스가 받는 인수를 제어하고 싶을 때

```bash
web: java -Xms256m -jar server.jar 
cache: java -jar mycache.jar
web_foo: java -jar other.jar
```

- 위와 같이 `Procfile`을 생성해서 압축하여 배포하면 됩니다.
- 애플리케이션의 기본 Jar를 실행하는 명령 이름은 `web`으로 지정해야 합니다.
- 또한 기본 Jar에 대한 설정은 `Procfile`에 첫 번째 줄에 있어야 합니다.
- 다음 명령으로 압축 후 배포하면 됩니다.
    - `zip -r application.zip .platform application.jar Procfile`

### 참고 자료

- [https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/java-se-procfile.html](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/java-se-procfile.html)
- [https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/platforms-linux-extend.html](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/platforms-linux-extend.html)
- [https://techblog.woowahan.com/2539/](https://techblog.woowahan.com/2539/)