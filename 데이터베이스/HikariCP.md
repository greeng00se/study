### DB

max_connections: client와 맺을 수 있는 최대 connection 수

wait_timeout: connection이 inactive 할 때(idle 상태가 아닌 비정상적인 상태) 다시 요청이 오기까지 얼마의 시간을 기다린 뒤에 close 할 것인지를 결정

### Hikari CP

autoCommit

- 풀에서 반환된 커넥션의 기본 자동 커밋
- 기본값: true

connectionTimeout

- 클라이언트가 풀에서 연결을 기다리는 최대 시간(밀리초). 
- 연결을 사용할 수 없는 상태에서 이 시간을 초과하면 SQLException이 발생
- 허용되는 최소 연결 시간 제한은 250밀리초. 
- 기본값: 30000(30초)

idleTimeout

- 풀에서 연결이 유휴 상태로 유지될 수 있는 최대 시간
- 이 설정은 minimumIdle이 maximumPoolSize보다 작도록 정의된 경우에만 적용. 
- 풀이 minimumIdle에 도달하면 유휴 연결은 삭제되지 않는다. 

기본값: 600000(10분)

minimumIdle: pool에서 유지하는 최소한의 Idel connection 수
maximumPoolSize: pool이 가질 수 있는 최대 connection 수(idle + in-use)

예) Idle connection 수가 minimumIdle보다 작고 전체 connection 수가 maximumPoolSize보다 작으면 추가로 connection 생성

권장사항 minimumIdle = maximumPoolSize -> 고정

참고 자료

- https://www.baeldung.com/java-connection-pooling
- https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#data.sql.datasource.connection-pool
- https://hudi.blog/dbcp-and-hikaricp/

- https://techblog.woowahan.com/2664/
- https://techblog.woowahan.com/2663/