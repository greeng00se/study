사용한 자원을 반납하는 방법에는 어떤 것이 있는지 알아보자.

### try-finally를 이용한 자원 반납

```java
MyResource myResource = new MyResource();
try {
    myResource.doSomething();
} finally {
    myResource.close();
}
```

try-with-resources 기능이 나오기 전에는 자원을 사용하고, finally 구문에서 반납했다.

### try-with-resources

```java
try (MyResource myResource = new MyResource();) {
    myResource.doSomething();
}
```

자바 7 부터 사용이 가능하다.

사용하려는 자원이 `AutoCloseable` 인터페이스를 구현해야 한다.

try 구문 안에 자원을 선언하고, try 구문이 종료되는 경우 자동으로 반납을 해주는 기능이다.

- 반납은 생성의 역순으로 진행된다.

### try-finally와 비교했을 때의 장점

코드의 가독성이 좋아진다.

일일히 자원에 대한 반납 작업을 하지 않아도 된다.

개발자의 실수나, 에러로 인해 자원을 반납하지 못하는 경우를 방지할 수 있다.

발생한 에러에 대한 스택 트레이스를 남겨, 더욱 유용한 예외 정보를 확인할 수 있다.

### 에러에 대한 스택 트레이스 예시

try-finally를 사용하여 자원 반납을 하는 경우, close에서 발생한 예외만 표시되고 readLine에서 발생한 예외는 표시되지 않는다.

```java
final MyResource myResource = new MyResource();
try {
    myResource.readLine();
} finally {
    myResource.close();
}

Exception in thread "main" java.lang.IllegalArgumentException: CLOSE
	at herb.MyResource.close(MyResource.java:24)
	at herb.MyResource.main(MyResource.java:10)
```

try-with-resources를 사용하면 Suppressed 꼬리표를 달리고, 발생한 모든 예외가 표시된다.

```java
try (final MyResource myResource = new MyResource()) {
    myResource.readLine();
}

Exception in thread "main" java.lang.IllegalArgumentException: READ
	at herb.MyResource.readLine(MyResource.java:19)
	at herb.MyResource.main(MyResource.java:14)
	Suppressed: java.lang.IllegalArgumentException: CLOSE
		at herb.MyResource.close(MyResource.java:24)
		at herb.MyResource.main(MyResource.java:13)
```

### 참고자료

Effective Java 9. try-finally보다는 try-with-resources를 사용하라

[try-with-resources, Baeldung](https://www.baeldung.com/java-try-with-resources)