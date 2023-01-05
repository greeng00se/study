[https://www.youtube.com/watch?v=DQFv0AxTEgM](https://www.youtube.com/watch?v=DQFv0AxTEgM)

### 웹 환경에서 인증과 인가

인증(Authentication) → 사용자의 신원을 확인하는 것

인가(Authorization) → 사용 권한을 부여하는 것

### OAuth 1.0

Resource Owner → 사용자

OAuth Client → 오너의 리소스를 활용하는 애플리케이션

OAuth Server → 오너의 신분을 인증하고, 역할을 인가한다.

흐름

클라이언트 → OAuthServer 주소 요청 → OAuthServer Client로 주소 반환 → 오너에게 리다이랙션 → 오너가 권한 인가 → OAuthServer가 클라이언트에게 리다이랙션 → Client와 Server 정보 교환

문제점

- 스코프 개념 없음
- 토큰 유효기간 문제
- 역할이 확실히 나누어지지 않음
- 클라이언트 구현 복잡
- 제한적인 사용환경

1.0 웹 환경 → 기존의 웹

### OAuth 2.0의 문제점 개선

스코프 기능 추가

- 1.0에서는 토큰만 있으면 모든 정보 접근 가능
- 스코프를 통해 해당 토큰의 접근 범위를 표현 가능

클라이언트 복잡성 간소화

- 1.0에서는 보안성을 위해 암호학적 기반으로 서명(복잡성 증가)
- Bearer Token + TLS 통해 해결
    - 해당 토큰을 소유를 하는 것 만으로도 권한 얻을 수 있음

서버의 역할이 확실히 나누어지지 않음

- 1.0에서는 서버의 역할이 다음과 같음 → 리소스 오너 인증, 인가 토큰 발급, 보호된 리소스 관리
- 2.0에서는 리소스 관리를 Resource Server로 분리

토큰 탈취 문제 개선

- 1.0에서는 토큰의 유효기간이 매우 길었다.
- 2.0에서는 Refresh Token 개념 생성
- 리소스 접근은 Access Token으로 접근
- 기한이 만료되면 Refresh Token을 이용하여 Access Token 재발급

제한적인 사용환경

- 1.0에서는 웹 환경 제한적
- Grant 개념을 추가 → 다양한 사용환경에 대한 flow를 나타내는 인증 방식
- Grant 종류
    - Authorization Code
        - 1.0과 같은 flow
    - Implicit → 웹 브라우저의 자바스크립트 기반
        - 리소스가 제한되어있을 때 간략한 방법으로 로그인 인증후에 토큰 발급 받음
    - Resource owner password credentials
        - 직접적으로 유저의 아이디와 비밀번호를 받아 요청
        - 기기의 OS와 같은 안전한 환경에서 사용
    - Client credentials
        - 리소스 오너와 OAuth Client가 동일한 개체일 때 사용

2.0 웹 환경 → 브라우저, JS, 스마트 기기

### OAuth 2.1

OAuth 2.0이 유명해짐에 따라 보안적으로 민감한 사용처들의 프로토콜 채택

- 금융, 병원 등등
- 2.1은 2.0의 모범 구현 사례를 스펙화를 시켰다.

2.1의 Grant

- Implicit, password credentials 제거
- Device Authorization Grant 추가
    - IOT 스마트 기기에 적합한 Grant
    - 타 스마트 기기를 사용하는 인증 Flow

OAuth 2.1 PKCE

- Authorization code grant에 추가된 PKCE
- 시작 요청에 해시값을 보내고 최종적으로 토큰을 받는 요청을 할 때 해시값의 원문을 보낸다.
- 중간자가 첫 요청을 탈취하더라도 해시값을 역연산 불가능함으로 안전

BCP

- Refresh Token 회전 방식
- Refresh Token이 1회성이 된다.
- 새로운 Access Token을 받으면 새로운 Refresh Token을 발급한다.

그외 보안사항

- Redirect URI 패턴 매칭 스펙아웃, 정확한 매칭 강제
- URI QueryString에서 Bearer 토큰 사용 불가능
- mTLS, DPoP 채택

2.1 웹 환경 → 스마트카, IOT 등등

### OpenID Connect 인증 계층

OAuth로 인증을 할 수 있나요? → 인가 권한에 대한 인증을 한 것 뿐

### OpenID Connect

다음 정보를 포함하는 JWT 기반의 ID토큰을 받음

- 토큰 발급자, 사용자 식별자, 토큰 사용자, 만료 시간, 토큰 발급 시간

2.0에서 사용자의 정보를 알아보기 위해서는 토큰을 가지고 API 콜을 해야했었다.

OpenID Connect를 사용하면 API 콜을 하지 않고 사용자의 정보를 가지고 올 수 있다.

OpenID Connect 웹 환경 → 트래픽이 늘어나서 인증서버 역부족 통신 부하를 줄이기 위해 생김

### GNAP*(Grant Negotiation and Authorization Protocol)

미래의 인증/인가 방식? 현재 개발 진행중

Grant 개념 → Interaction 상호작용

동적인 JSON 객체를 통해 사용자와 상호작용에 대한 부분을 정하고 시작

Resource Owner가 나눠짐

- GNAP Resource Owner → 리소스 접근 인가
- GNAP End User → Client와 상호작용

접근 범위 정밀 제어 및 여러 토큰 요청

JWK, 인증서 기반 Client 보안 내장

Open ID 신기술 연동성

### 요약

1.0 → Oauth 인가 프로토콜 개념 제시, 브라우저 환경 동작

2.0 → Client 간소화, Refresh Token 등장, Grant 추가

2.1 → 보안성 개선, IoT 기기 지원, 모범 사례 스펙

OpenID → 인증 계층 추가, Api 호출 없는 사용자 정보 확인

GNAP → Interaction 확장, 신기술 호환(확장 스펙 제시)