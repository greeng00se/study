### Job

- 배치 계층 구조에서 가장 상위 개념
- 배치 작업 자체를 의미
- 여러 Step을 포함하고 있는 컨테이너로 반드시 한 개 이상의 Step으로 구성해야 함
- 하나의 명세서

### 기본 구현체

- SimpleJob → 순차적으로 Step을 실행
- FlowJob → 특정한 조건과 흐름에 따라 Step을 구성하여 실행, Flow 객체 실행

### Jop

Jop ← AbstractJob ← [SimpleJop, FlowJop]