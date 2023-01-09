### Java - record

JDK 14 Preview, JDK 16 공식 지원

생성자, getter, toString, Equals & HashCode 자동 생성

final로 되기 때문에 setter 사용 불가

```java
record Developer(String name, int age) {}
```

### Kotlin - data class

생성자, getter, toString, Equals & HashCode, copy 자동 생성

var로 선언한다면 값 변경 가능

```kotlin
data class Developer(
    var name: String,
    var age: Int,
)
```

### Java - sealed class

JDK 15 Preview, JDK 17 공식 지원

상속 및 구현할 수 있는 클래스 지정

```java
sealed class Hero permits Batman, Superman, Spiderman {}
```

지정하지 않은 클래스들은 상속 불가능

같은 패키지 내의 확장만 허용

### Kotlin - sealed class

같은 패키지 내에서 확장 허용

```java
public sealed class Hero {
    class Batman : Hero()
    class Superman : Hero()
    class Spiderman : Hero()
}
```

### Java - pattern matching for instanceof

JDK 14 Preview, JDK 16 공식 지원

### Kotlin - is

타입 검사와 캐스팅을 진행하는 키워드

### Java - pattern matching for switch

jDK 17 Preview

siwtch문을 이용한 패턴 매칭

### Kotlin - when

siwtch에 해당하는 구성 요소

값을 만들어내는 식으로 사용 가능

### Java - Stream.toList

JDK 16 공식 지원

최종 연산자 → UnmodifiableList 반환

### Kotlin Collections, Sequence

확장 함수

Collections 확장함수는 순차적으로 즉시 연산 진행

지연 연산의 이점이 필요한 경우 Sequence 사용하는 것이 유리

### Java - text block

JDK 13 Preview, JDK 15 공식 지원

한줄 이상의 문자열을 사용 → “””로 감싸기

### Kotlin text block

자바와 동일하지만 인덴트를 제거하는 trimIndent() 사용해야 한다.

### Kotlin null 처리

null을 안전하게 처리하기 위한 타입 지원

?., !!, let, elvis operator

### Kotlin 확장 함수

어떤 클래스의 멤버 메서드인 것처럼 외부에서 함수 추가 가능

라이브러리에서 기존에 정의된 클래스에 함수를 추가 가능

상속, 조합 없이 클래스 확장 가능

오버라이딩 불가능

### kotlin Operator overloading

+, - 등과 같은 연산자 오버로딩

`operator fun`

### Coroutine

비동기 코드를 편리하게 작성할 수 있음

비선점형 멀티태스킹을 위해 서브 루틴을 일반화하는 컴퓨터 프로그램 구성 요소

컨텍스트 스위칭이 없는 동시성 프로그래밍 지원

### Java 17 이후 주요 업데이트

JDK18 → 표준 자바 API 기본 문자셋 UTF-8