# 데이터 접근 기술2 - 예외 변환과 @Repository

### @Repository의 기능

- 컴포넌트 스캔의 대상이 된다.
- 예외 변환 AOP의 대상이 된다.
- 스프링은 JPA를 함께 사용하는 경우 JPA 예외 변환기를 등록한다.
    - `PersistenceExceptionTranslator`
    - JPA 관련 예외가 발생하면 스프링 데이터 접근 예외로 변환한다.

### PersistenceExceptionTranslationPostProcessor

- 스프링 부트는 해당 전처리기를 자동으로 등록한다.
- `@Repository`를 AOP 프록시를 만드는 역할을 한다.