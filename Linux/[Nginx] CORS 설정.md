### 개요

- CORS 설정을 하지 않아서 배포한 웹 사이트에서 백엔드 API로 접근을 못하는 문제가 발생했습니다.
- 프론트 개발시에는 Vite를 이용하여 프록시를 설정하였고, 모든 요청이 프록시를 경유하도록 설정하였습니다.
    - 서버와 서버 사이의 요청에는 CORS가 걸리지 않습니다.
- 프론트는 Cloudflare Pages를 사용하여 정적 웹 페이지 형태로 배포를 했고 Cloudflare Pages에서 _redirects 파일을 통한 프록시를 아직 지원하지 않기에 서버쪽에서 설정을 하였습니다.
- 현재 백엔드는 Nginx에 리버스 프록시를 설정해두었기 때문에 Nginx에 CORS 관련 내용을 설정하였습니다.

### 교차 출처 리소스 공유(Cross-Origin Resource Sharing, CORS)

> 추가 HTTP 헤더를 사용하여, 한 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제입니다.
- Mozilla Developer Network
> 

### 환경

- Azure VM Ubuntu 20.04.4 LTS
    - nginx/1.18.0 (Ubuntu)

### Nginx 설정

- `/etc/nginx/conf.d/default.conf` 에 해당 내용을 설정하였습니다.

```bash
server {
    server_name api.booklog.dev;

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
        proxy_pass http://localhost:3000/;
    }

    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/api.booklog.dev/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/api.booklog.dev/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}
```

- Content-Type이 application/json인 경우 실제 요청을 전송하기 전 OPTIONS 메서드를 이용하여 요청이 안전한지 확인합니다. 이를 사전 요청(Preflighted Request)이라고 합니다.
- 사전 요청에 관해서는 204 No Content를 반환하는 것을 권장하고 있습니다.
- 백엔드 API의 일부 요청에 Bearer 토큰을 이용한 인증을 요구함으로 Authroization 헤더도 허용하도록 설정했습니다.
- 설정을 마친 후 `nginx -t` 명령어를 사용하여 설정 파일이 정상적인지 체크 후 `nginx -s reload` 명령어로 nginx 서버를 재구동하였습니다.

## 참고 자료

- [CORS, MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
- [CORS, IBM](https://www.ibm.com/docs/ko/integration-bus/10.0?topic=overview-cross-origin-resource-sharing)
- [Preflight request, MDN](https://developer.mozilla.org/ko/docs/Glossary/Preflight_request)
- [https://serverfault.com/questions/162429/how-do-i-add-access-control-allow-origin-in-nginx](https://serverfault.com/questions/162429/how-do-i-add-access-control-allow-origin-in-nginx)
- [Redirects, Cloudflare Pages Docs](https://developers.cloudflare.com/pages/platform/redirects/)