> Joshua Bloch님의 Effective Java 3/E를 읽고 정리한 글입니다.
>

## 생성자 대신 정적 팩터리 메서드를 고려하라

- 일반적으로 클래스의 인스턴스를 얻는 방법으로 생성자를 사용하는 방법이 있다.
- 생성자를 사용하는 방법 이외에도 정적 팩터리 메서드를 이용해 인스턴스를 얻는 방법도 있다.

> 📌 정적 팩터리 메서드(static factory method)
> 
> - 객체 생성을 흔히 사용하는 생성자가 아닌 정적 메서드로 하는 것을 말한다.

```java
public static Boolean valueOf(boolean b) {
		return (b ? TRUE : FALSE);
}
```

- Boolean 클래스의 정적 팩터리 메서드인 `valueOf()`

## 정적 팩터리 메서드의 장점

### 장점 1. 이름을 가질 수 있다.

- 생성자를 사용하면 반환하는 객체의 특성을 제대로 설명하지 못한다.
- 반환될 객체의 역할과 특성을 표현하는 메서드명을 가질 수 있다.

### 장점 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

- 변경이 필요하지 않은 클래스는 인스턴스를 미리 생성해두거나, 새로 생성한 인스턴스를 캐싱하여 다시 활용하는 방법으로 불필요한 객체의 생성을 막을 수 있다.
- 대표적으로 `Boolean.valueOf(boolean)` 메서드는 Boolean 클래스에서 미리 만들어둔 객체를 반환한다.
- 언제 어느 인스턴스를 살아 있게 할 지 통제할 수 있고 이러한 클래스를 인스턴스 통제 클래스라고 한다.
    - 플라이웨이트 패턴의 근간이 된다.

> 📌 플라이웨이트 패턴(flywieght pattern)
> 
> - 어떤 클래스의 인스턴스 하나로 여러 개의 가상 인스턴스를 제공하여 객체를 가볍게 만들어 메모리의 사용을 줄이는 패턴이다.
> - 인스턴스가 많이 필요하고 같은 방식으로 제어할 때 유용하게 쓰이지만 특정 인스턴스만 다른 인스턴스와 다르게 행동하게 할 수 없다는 단점이 있다.

### 장점 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

- 반환 타입에 호환 가능한 다른 타입을 반환할 수 있기 때문에 유연함이 생긴다.
- API를 만들 때 구현 클래스를 공개하지 않고도 객체를 반환할 수 있어 인터페이스 기반의 프레임워크를 사용할 수 있게 해준다.
    - 클라이언트 코드로부터 인터페이스 기반의 코드를 사용하도록 강제할 수 있다.
    - 즉 API를 사용하는 입장으로써는 인터페이스대로 동작하는 객체를 얻을 것을 알기에 익혀야 될 정보의 수가 줄어든다.

### 장점 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

- 클라이언트는 팩터리가 건내주는 객체가 반환 타입의 하위 타입이기만 하면 어떤 클래스의 인스턴스인지 알 수 도 없고, 알 필요도 없다.

```java
public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
    Enum<?>[] universe = getUniverse(elementType);
    if (universe == null)
        throw new ClassCastException(elementType + " not an enum");

    if (universe.length <= 64)
        return new RegularEnumSet<>(elementType, universe);
    else
        return new JumboEnumSet<>(elementType, universe);
}
```

- 입력 매개변수에 따라 반환값이 달라지는 `EnumSet.noneOf()`

```java
public interface Drink {

    static Drink of(String name) {
        if (name.equals("coffee")) {
            return new Coffee();
        } else {
            return new Juice();
        }
    }

}

public class Juice implements Drink {}
public class Coffee implements Drink {}
```

- 매개변수로 전달되는 문자열에 따라 다른 객체를 반환하는 정적 팩터리 메서드 예시

### 장점 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

- 반환값이 인터페이스여도 되기 때문에 해당 팩터리 메서드의 변경 없이 구현체를 변경할 수 있다.
- 이 유연함은 서비스 제공자 프레임워크를 만드는 근간이 된다.
    - 서비스 제공자 프레임워크의 대표적인 예시는 JDBC가 있다.

> 📌 서비스 제공자 프레임워크(service provider framework)
> 
> - 다양한 서비스 제공자들이 하나의 서비스를 구성하는 시스템이다.
> - 서비스 제공자 인터페이스 패턴이라고도 불리며 확장 가능한 애플리케이션을 만드는 방법을 제공하는 것이 목적이다.
> - 3개의 핵심 컴포넌트인 서비스 인터페이스, 제공자 등록 API, 서비스 접근 API로 이루어진다.
> - 해당 패턴의 변형으로는 브리지 패턴, DI 프레임워크등이 있다.

| 컴포넌트 | 설명 | 예시 |
| --- | --- | --- |
| 서비스 인터페이스 | 구현체의 동작을 정의한다. | Connection |
| 제공자 등록 API | 제공자가 구현체를 등록할 때 사용한다. | Drivermanager.registerDriver |
| 서비스 접근 API | 클라이언트가 서비스의 인스턴스를 얻을 때 사용한다. | DriverManager.getConnection |
| 서비스 제공자 인터페이스 | 서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체를 설명해준다. | Driver |

## 정적 팩터리 메서드의 단점

### 단점 1. 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.

- 상속을 하려면 public이나 protected 생성자가 필요하기 때문이다.

### 단점 2. 프로그래머가 찾기 힘들다.

- 생성자와 달리 API 설명에 명확하게 나오지 않으니 직접 찾아서 사용해야 한다.
- 꾸준히 사용된 규약을 따라 메서드 명을 지어 이를 완화할 수 있다.

## 정적 팩터리 메서드의 명명 방식

| 명명 방식 | 설명 | 예시 |
| --- | --- | --- |
| from() | 매개변수를 받아 해당 타입의 인스턴스를 반환 | Date.from() |
| of() | 여러 매개 변수를 받아 적합한 타입의 인스턴스 반환 | EnumSet.of() |
| valueOf() | from(), of()의 자세한 버전 | BigInteger.valueOf() |
| instance(), getInstance() | 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지 않는다. | StackWalker.getInstance() |
| create(), newInstance() | 매번 새로운 인스턴스를 반환함을 보장한다. | Array.newInstance() |
| getType() | 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 사용한다. Type은 팩터리 메서드가 반환할 객체의 타입이다. | Files.getFileStore() |
| newType() | getType()의 새로운 인스턴스를 반환하는 버전 | Files.newBufferdReader() |
| type() | getType(), newType()의 간결한 버전 | Collections.list() |