### Spock이란?

- Spock은 Java 및 Groovy 애플리케이션을 위한 테스트 프레임워크입니다.
- 테스트 코드는 Groovy로 작성됩니다.
- Spock 2.0부터는 JUnit5를 사용합니다.

### 실행 환경

- Spring Boot 2.7.4
- Zulu 11

### build.gradle 설정

```groovy
plugins {
    ...
    // groovy plugin 사용
    id 'groovy'
}

repositories {
    mavenCentral()
    // jitpack.io를 저장소로 추가
    maven { url 'https://jitpack.io' }
}

dependencies {
    // spock framework를 위한 의존성
    testImplementation 'org.spockframework.spock:spock-core:spock-2.3'
    testImplementation 'org.spockframework.spock:spock-spring:spock-2.3'
}
```

### 테스트 폴더 생성

![image](https://user-images.githubusercontent.com/58586537/193458707-c673cbfa-754f-4b2f-89ea-0abdb2b03c5b.png)

- test 폴더 아래 groovy 폴더를 따로 생성해줍니다.
- java 폴더 아래 groovy 파일을 생성하고 따로 추가 설정 없이  gradle로 테스트를 실행하는 경우 `No tests found for given includes` 에러가 발생합니다.

### Fixture Method

```groovy
def setupSpec() {}    // runs once -  before the first feature method
def setup() {}        // runs before every feature method
def cleanup() {}      // runs after every feature method
def cleanupSpec() {}  // runs once -  after the last feature method
```

- 위 메서드를 통해 환경을 설정하거나 정리할 수 있습니다.

### Blocks

- Spock에는 given, when, then, expect, cleanup, where 총 6개의 블록이 있습니다.
- 메서드에는 최소 하나의 블록이 존재해야 합니다.

### given, when, then

```groovy
def "1 + 2 = 3이다"() {
    given:
    long a = 1L
    long b = 2L

    when:
    def result = a + b

    then:
    result == 3L
    result != 2L

    when:
    def hello = "hello"

    then:
    hello == "hello"
}
```

- `given` → 설정 작업 수행, 생략 가능합니다.
- `when` → 테스트 코드를 실행하는 블록입니다.
- `then` → when과 then은 항상 함께 사용되며 then 블록에서 검증합니다.
- 메서드에는 여러 쌍의 `when-then` 블록을 사용할 수도 있습니다.

### expect, where

```groovy
def "두 숫자중 큰 숫자를 반환한다."() {
    expect:
    Math.max(a, b) == c

    where:
    a | b   || c
    1 | 2   || 2
    7 | 100 || 100
    4 | 5   || 5
}
```

- `expect` → 테스트 코드 실행과 검증을 한 번에 할 수 있습니다.
- `where` → 다양한 케이스를 한 번에 검증할 수 있도록 해줍니다.
    - when-then이나 expect에서 사용한 매개변수의 값을 설정하는 블록입니다.
    - where 블록은 항상 메서드의 최하단에 위치해야 합니다.
    - `| || ; ;;` 구분자를 사용할 수 있습니다.

### Exception Condition

```groovy
def "0으로 나눌 수 없다"() {
    when:
    1 / 0

    then:
    def e = thrown(ArithmeticException)
    e.message == "Division by zero"
}
```

- `thrown()` 메서드를 이용하여 예외를 검증할 수 있습니다.

### Mock

```groovy
def "사용자 전체 조회"() {
    given:
    def userRepository = Mock(UserRepository)
    def userService = new UserService(userRepository)
    userRepository.findAll() >> List.of(
            new User("user01", 10L),
            new User("user02", 15L)
    )

    when:
    List<UserResponseDto> result = userService.findAll()

    then:
    result.size() == 2
    result.get(0).name == "user01"
}
```

- 다음과 같이 Mock객체를 생성할 수 있습니다.
    - `def userRepository = Mock(UserRepository)`
    - `UserRepository userRepository = Mock()`
- Mock 객체의 반환 값은 `>>` 를 이용하여 설정할 수 있습니다.

### 정리

- 공식 문서와 spock에서 제공하는 예제 github에 더욱 자세한 사용 방법이 나와있습니다.
- 검증해야 될 케이스가 다양한 경우 spock을 사용하면 읽기 쉬운 단위 테스트를 작성할 수 있을 것 같습니다.

### 참고자료

- [https://github.com/spockframework/spock-example](https://github.com/spockframework/spock-example)
- [https://techblog.woowahan.com/2560/](https://techblog.woowahan.com/2560/)
- [https://spockframework.org/](https://spockframework.org/)
- [https://docs.gradle.org/current/userguide/groovy_plugin.html](https://docs.gradle.org/current/userguide/groovy_plugin.html)