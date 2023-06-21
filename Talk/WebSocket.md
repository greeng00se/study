### 웹 소켓이란?

두 프로그램 간의 메시지를 교환하기 위한 통신 방법 중 하나다.

웹 소켓을 지원하는 브라우저의 경우 웹 소켓 프로토콜을 지원한다.

### 웹 소켓의 특징

양방향 통신 (Full-Duplex)

- 데이터 송수신을 동시에 처리할 수 있는 통신 방법
- 클라이언트와 서버가 서로에게 원할 때 데이터를 주고 받을 수 있다.
- 통상적인 http 통신은 단방향 통신

실시간 네트워킹 (Real Time-Networking)

- 웹 환경에서 연속된 데이터를 빠르게 노출

### 웹 소켓 이전의 통신

Polling, Long Polling → 서버로 일정 주기 요청 송신

Streaming → 서버에 요청 보내고 끊임없이 응답을 받음

이 방법들은 HTTP를 통해 통신하기 때문에 Header가 불필요하게 크다.

### 웹 소켓 동작 방법 - 핸드쉐이킹

Get 메서드 사용, Http 버전 1.1 이상이어야 한다.

Upgrade

- 현재 프로토콜에서 다른 프로토콜로 업그레이드하기 위한 규칙
- Connection: Upgrade 설정해야 한다.

Sec-WebSocket-Key → 신원 인증용

Sec-WebSocket-Protocol → 서브 프로토콜 의미

응답 101 Switching Protocols가 Response로 온다면 웹 소켓이 연결된 것이다.

Sec-WebSocket-Accept: 클라이언트와 계산한 값과 같지 않으면 연결수립 x

이제 프로토콜이 ws(80)로 변경 wss(443)

Message 사용 → 여러 프레임이 모여서 구성하는 하나의 논리적 메시지 단위

프레임 → 작은 헤더 + payload로 구성

웹 소켓 통신에 사용되는 데이터는 UTF8 인코딩 사용

### 웹 소켓 프로토콜 특징

http 프로토콜 위에서 핸드쉐이킹한다. 따라서 http header 사용

기존의 http 포트를 사용한다

프레임으로 구성된 메시지라는 논리적 단위로 송수신한다.

메시지에 포함될 수 있는 교환 가능한 메시지는 텍스트와 바이너리 데이터 뿐이다.

### 웹 소켓의 한계

html5 이전의 기술로 구현된 서비스에서는 → Socket.io, SockJS 사용해야 함

STOMP(Simple Text Oriented Message Protocol)

- 채팅 통신을 하기 위한 형식을 정의
- 일반적으로 웹 소켓 위에서 사용
- 프레임 기반의 프로토콜
- CONNECT, SEND, SUBSCRIBE, DISCONNECT 등이 있음