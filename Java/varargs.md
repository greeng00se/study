### Varargs(가변인수)

자바 5에서 도입되었고, 명시한 타입의 인수를 0개 이상 받을 수 있는 기술이다.

### varargs 사용

varargs 사용시 다음과 같은 규칙을 지켜야 한다.

- 각 메소드는 하나의 varargs만 가질 수 있다.
- varargs는 항상 매개변수의 마지막에 위치해야 한다.

다음과 같이 타입 뒤에 줄임표를 이용하여 사용할 수 있고, 일반 배열을 사용하는 것 처럼 사용하면 된다.

```java
public void printNames(String... names) {
    for (String name : names) {
        System.out.println(name);
    }
}

printNames("Herb");
printNames("Herb", "Pepsi", "Zero", "Good");
```

### varargs 장점

인수의 개수가 일정하지 않은 메서드를 정의할 수 있다.

- 매개변수 개수에 따라 메서드를 하나하나 정의하지 않아도 된다.
- 예) `System.out.printf`, `String.format`, `List.of()`

메서드에 넘기는 값을 클라이언트가 조절할 수 있게 해주기 때문에 유연하다.

### varargs 단점

varargs는 호출될 때마다 입력받은 매개변수의 개수와 같은 크기의 배열을 새로 만들어 할당하고, 초기화한다.

따라서 성능에 매우 민감한 상황인 경우 문제가 발생할 수 있다.

### 성능 문제 해결 방법

우리가 자주 사용하는 `List.of()` 메서드의 경우 이 문제를 아주 멋진 방법으로 해결하였다.

아래와 같이 다중정의를 하는 패턴을 이용하여 성능 문제를 해결할 수 있다.

- 받아야 하는 값이 적은 경우 → 다중정의한 메서드 사용
- 받아야 하는 값이 많은 경우 → varargs를 받을 수 있는 메서드 사용

```java
// List.of()
static <E> List<E> of(E e1) {
    return new ImmutableCollections.List12<>(e1);
}

static <E> List<E> of(E e1, E e2) {
    return new ImmutableCollections.List12<>(e1, e2);
}

static <E> List<E> of(E e1, E e2, E e3) {
    return new ImmutableCollections.ListN<>(e1, e2, e3);
}

static <E> List<E> of(E e1, E e2, E e3, E e4) {
    return new ImmutableCollections.ListN<>(e1, e2, e3, e4);
}

static <E> List<E> of(E... elements) {
    switch (elements.length) {
        case 0:
            return ImmutableCollections.emptyList();
        case 1:
            return new ImmutableCollections.List12<>(elements[0]);
        case 2:
            return new ImmutableCollections.List12<>(elements[0], elements[1]);
        default:
            return new ImmutableCollections.ListN<>(elements);
    }
}
```

### 참고자료

[Varargs in Java, Baeldung](https://www.baeldung.com/java-varargs)

Effective Java 53. 가변인수는 신중히 사용하라

### 추가 varargs 관련 키워드

Effective Java 32. 제네릭과 가변인수를 함께 쓸 때는 신중하라

- Heap Pollution(힙 오염)
- @SafeVarargs