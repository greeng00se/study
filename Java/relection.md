### Reflection

- 언어이자 JVM 기능,
- 실행하는 동안 앱 클래스와 객체 정보를 추출할 수 있다.
- 대표적으로 JUnit, Spring, Jackson 등 외부 라이브러리가 reflection을 사용한다.

### Java Reflection API 진입점

- `Class<?>` 객체를 얻는 방법
    - `Object.getClass()`
    - 타입 이름에 `.class` 추가하기
    - `Class.forName()` 동적으로 패키지명을 포함한 네임을 이용하여 찾을 수 있음
- forName을 사용하는 것이 가장 위험하다.
    - 이름을 잘못 입력할 경우 `ClassNotFoundException` 발생
- 그렇지만 사용해야 하는 경우
    - 사용자 정의 파일에서 설정을 가져오는 경우 ex) bean class
    - 코드를 컴파일 할 때 클래스가 없는 경우

### Constructor<?>

- 클래스 생성자의 모든 정보를 포함한다.
- 매개변수의 타입이나 개수에 한정되지 않는다.
- `Constructor.newInstance()`
    - 인자를 받아 적절한 생성자가 있다면 인스턴스를 반환한다.
- `Class.getDeclaredConstructors()`
    - 모든 생성자 반환
- `Class.getConstructors()`
    - public 생성자만 반환

### Field

- `Class.getDeclaredFields()`
    - 현재 클래스에 선언된 필드 배열을 반환한다.
    - 상속된 필드를 제외한 모든 필드를 반환한다.
- `Class.getField()`
    - public 필드를 반환한다.
- `Field.isSyntetic()`
    - Synthetic Field 보이지 않는 필드