# [Nginx] SSL, 리버스 프록시 설정하기

### 개요

- 현재 프론트엔드 애플리케이션의 경우 Cloudflare Pages를 이용하여 배포해 사용중입니다.
- 프론트엔드에서 리버스 프록시 서버를 통해 백엔드 API에 접근할 수 있도록 하려고 합니다.
    - 리버스프록시 서버에는 SSL을 적용하고 하위서버들은 SSL을 적용하지 않은 상태로 통신할 수 있습니다.

### 환경

- Azure VM Ubuntu 20.04.4 LTS
    - nginx/1.18.0 (Ubuntu)
- Cloudflare에 설정된 도메인

### Nginx 설치

```bash
sudo apt update
sudo apt-get install nginx
```

- `/etc/nginx/`경로에 nginx 관련 파일들이 설치됩니다.
- `nginx -v` 명령어를 사용해 버전을 확인할 수 있습니다.

### Certbot 이용하여 인증서 발급

- [certbot 홈페이지](https://certbot.eff.org/)에 들어가서 웹 서버 환경이랑 OS를 선택하면 설치 안내 페이지가 나옵니다.
- Nginx랑 Ubuntu 20을 선택하여 설치 진행했습니다.
- `/etc/letsencrypt/live/도메인` 폴더 아래 인증서가 발급됩니다.
- 발급된 인증서는 `sudo certbot renew --dry-run` 명령어를 이용하여 자동 갱신 테스트를 할 수 있습니다.

### A 레코드 설정

![image](https://user-images.githubusercontent.com/58586537/183416686-2cc5d9e0-6d39-41a0-94fb-9509781c23f2.png)

- 백엔드 API의 주소를 `api.booklog.dev` 로 설정하기 위해 A 레코드를 현재 nginx가 설치된 인스턴스의 IP로 설정했습니다.
- Proxy status를 이용한다면 프록시 호스트에 대한 요청이 Cloudflare로 이동한 다음 원본 서버로 전달됩니다.
    - Cloudflare가 제공하는 기능(모든 요청을 최적화, 캐싱, DDos 공격에 대한 보호)을 사용 할 수 있지만 무료 플랜의 경우 국내 엣지를 사용하지 않아 더 느려질 수 있다고 합니다.

### Nginx 설정

- `/etc/nginx/conf.d` 폴더안에 `default.conf` 파일을 만들어 아래와 같이 설정했습니다.
    - 기본 nginx 설정파일에 해당 폴더 안에 있는 모든 conf 파일의 설정을 Import하는 구문이 있습니다.

```bash

server {
    server_name api.booklog.dev;

    location / {
        proxy_pass http://localhost:3000/;
    }

    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/api.booklog.dev/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/api.booklog.dev/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}
```

- location을 통해 리버스 프록시 설정을 할 수 있습니다.
    - 백엔드 API는 nest.js로 작성되어 있으며 현재 동일 서버에 3000번 포트에서 작동하고 있습니다.
    - 헤더를 변경한다던지 접근하는 uri에 따라 다른 서버로 요청을 전달 할 수도 있습니다.
- certbot으로 발급한 인증서에 대한 정보도 설정합니다.
- 위와 같이 설정하게 되면 `api.booklog.dev`로 들어오는 모든 요청을 프록시가 받아 백엔드 API에 전달하고 해당 요청에 대한 응답을 클라이언트에게 전달합니다.

### Nginx 설정 파일 체크와 리로딩

- 모든 설정을 마친 후 `sudo nginx -t` 명령어를 사용하여 설정파일을 체크할 수 있습니다.
- 체크를 한 후 `sudo nginx - reload` 명령어를 사용하여 리로딩할 수 있습니다.

### 참고

- [클라우드 플레어(Cloudflare) 를 DNS 서버로 사용하기](https://www.lesstif.com/system-admin/cloudflare-dns-128122955.html)
- [Install nginx, Nginx](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)