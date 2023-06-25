
### HTTP, 웹소켓

HTTP

- 비 연결성
- 연결을 맺고 끊는 과정의 비용
- 요청 - 응답 구조

웹 소켓

- 연결 지향
- 한번 연결 맺은 뒤 유지
- 양방향 통신

### Spring-WebSocket

Configuration

- @EnableWebSocket
- implements WebSocketConfigurer

Socket 핸들러 + 웹소켓 연결 주소가 필요  
핸들러의 경우 TextWebSocketHandler or BinaryWebSocketHandler를 구현해야 한다.

setAllowedOrigins -> allow
withSocketJS -> Socket js

### WebSocketSession

- 웹 소켓이 연결될 때 생기는 연결정보를 가지고 있는 객체
- 컬렉션으로 관리하는 경우가 많음

### STOMP(Simple Text Oriented Messaging Protocol)

메시지 브로커를 활용하여 쉽게 메시지를 주고 받을 수 있는 프로토콜

- Pub-Sub(발행-구독) 발신자가 메시지를 발행하면 수신자가 그것을 수신하는 메시징 패러다임
- 메시지 브로커: 발신자의 메시지를 받아와서 수신자들에게 메시지를 전달하는 어떤 것

웹소켓 위에 얹어 함께 사용할 수 있는 서브 프로토콜

프레임 단위의 프로토콜

STOMP의 형식

```
COMMAND
header1:value1
header2:value2

Body^@
```

### 설정

- @EnableWebSocketMessageBroker
- WebSocketMessageBrokerConfigurer

enableSimpleBroker 
- 내장 브로커 사용
- prefix가 붙은 메시지 발생 시 메시지 브로커가 처리

-> queue 1:1
-> topic 1:N

setApplicationDestinationPrefixes()

- 메시지 핸들러로 라우팅 되는 Prefix

@MessageMapping -> RequestMapping과 유사  
@SendTo -> 반환된

addEndPoint()

- 웹 소켓 연결 주소
- 이전처럼 핸들러 하나하나 추가하지 않아도 된다.

### STOMP 장점

하위 프로토콜 혹은 컨벤션을 따로 정의하지 않아도 된다  
연결 주소마다 새로 핸들러를 구현하고 설정해줄 필요가 없다  
외부 메시징 큐를 사용할 수 있다 RabbitMQ, Kafka  
Spring Security를 사용할 수 있다.  