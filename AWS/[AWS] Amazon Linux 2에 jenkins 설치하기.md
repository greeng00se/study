### 개요

- Amazon Linux 2 Kernel 5.10 AMI로 생성한 인스턴스에 Jenkins를 설치합니다.

### Amazon Linux Jenkins 설치

```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum upgrade
# amazon-linux-extras로 jdk 설치
sudo amazon-linux-extras install java-openjdk11
sudo yum install jenkins
sudo systemctl daemon-reload
```

- Jenkins 공식 홈페이지에서 안내하는 설치 방법에서 openjdk 부분만 amazon-linux-extras를 이용하여 설치합니다.

### jenkins 시작하기

```bash
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

- 인스턴스가 재시작을 하더라도 젠킨스가 동작할 수 있게 enable 해줍니다.

### nginx 설치

```bash
sudo amazon-linux-extras install nginx1
```

- amazon-linux-extras를 이용하면 nginx를 간단하게 설치할 수 있습니다.

### nginx 리버스 프록시 설정

```bash
server {
    listen        80;
    server_name _;
    location / {
        proxy_pass         http://localhost:8080;
    }
}
```

- Jenkins는 8080 포트로 동작하기 때문에 리버스 프록시 설정해줍니다.
- `/etc/nginx/conf.d` 아래 `default.conf` 파일을 하나 생성하고 위와 같이 입력하고 저장합니다.
- nginx의 기본 설정 파일이 `include /etc/nginx/conf.d/*.conf;` 설정 때문에 `.conf` 로 끝난다면 설정이 적용됩니다.
- 설정 후 `sudo systemctl restart nginx` 명령어로 nginx를 재시작해줍니다.

### 참고 자료

- [https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos](https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos)