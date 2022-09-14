# Amazon Linux 2에 jenkins 설치하기

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
upstream jenkins {
  keepalive 32; # keepalive connections
  server 127.0.0.1:8080; # jenkins ip and port
}

# Required for Jenkins websocket agents
map $http_upgrade $connection_upgrade {
  default upgrade;
  '' close;
}

server {
  listen          80;       # Listen on port 80 for IPv4 requests

  server_name     jenkins.example.com;  # replace 'jenkins.example.com' with your server domain name

  # this is the jenkins web root directory
  # (mentioned in the output of "systemctl cat jenkins")
  root            /var/run/jenkins/war/;

  # pass through headers from Jenkins that Nginx considers invalid
  ignore_invalid_headers off;

  location ~ "^/static/[0-9a-fA-F]{8}\/(.*)$" {
    # rewrite all static files into requests to the root
    # E.g /static/12345678/css/something.css will become /css/something.css
    rewrite "^/static/[0-9a-fA-F]{8}\/(.*)" /$1 last;
  }

  location /userContent {
    # have nginx handle all the static requests to userContent folder
    # note : This is the $JENKINS_HOME dir
    root /var/lib/jenkins/;
    if (!-f $request_filename){
      # this file does not exist, might be a directory or a /**view** url
      rewrite (.*) /$1 last;
      break;
    }
    sendfile on;
  }

  location / {
      sendfile off;
      proxy_pass         http://jenkins;
      proxy_redirect     default;
      proxy_http_version 1.1;

      # Required for Jenkins websocket agents
      proxy_set_header   Connection        $connection_upgrade;
      proxy_set_header   Upgrade           $http_upgrade;

      proxy_set_header   Host              $host;
      proxy_set_header   X-Real-IP         $remote_addr;
      proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header   X-Forwarded-Proto $scheme;
      proxy_max_temp_file_size 0;

      #this is the maximum upload size
      client_max_body_size       10m;
      client_body_buffer_size    128k;

      proxy_connect_timeout      90;
      proxy_send_timeout         90;
      proxy_read_timeout         90;
      proxy_buffering            off;
      proxy_request_buffering    off; # Required for HTTP CLI commands
      proxy_set_header Connection ""; # Clear for keepalive
  }

}
```

- Jenkins는 8080 포트로 동작하기 때문에 리버스 프록시 설정해줍니다.
- 위 설정 파일은 공식 홈페이지에서 안내한 기본적인 설정 파일입니다.
- `/etc/nginx/conf.d` 아래 `default.conf` 파일을 하나 생성하고 위와 같이 입력하고 저장합니다.
- nginx의 기본 설정 파일이 `include /etc/nginx/conf.d/*.conf;` 설정 때문에 `.conf` 로 끝난다면 설정이 적용됩니다.
- 설정 후 `sudo nginx -t`로 설정파일이 정상인지 확인하고 `sudo systemctl restart nginx` 명령어로 nginx를 재시작해줍니다.

### 참고 자료

- [https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos](https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos)
- [https://www.jenkins.io/doc/book/system-administration/reverse-proxy-configuration-nginx/](https://www.jenkins.io/doc/book/system-administration/reverse-proxy-configuration-nginx/)