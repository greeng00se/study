### equals와 hashcode를 함께 정의해야 하는 이유?

hashCode를 재정의를 하지 않는 경우 Object의 기본 hashCode 메서드를 사용하게 된다.

따라서 논리적으로 같다고 하더라도 hashcode 메서드가 다른 값을 반환하기 때문에, hash를 사용하는 Collection 구현체를 사용할 때 문제가 발생한다.

### 추가로 동일한 hashcode 값을 반환하는 경우?

논리적으로 동일할 때 hashcode 메서드가 다른 값을 반환하면 문제가 생기니까, 그렇다면 다음과 같이 hashcode 값을 항상 동일하게 반환하도록 하면 문제가 없을까?

```java
@Override
public int hashCode() {
    return 404;
}
```

모든 경우에 동일한 hashcode를 반환하더라도 문제가 생긴다.

HashMap의 경우 해시 함수가 동일한 값을 반환하는 해시 충돌이 일어났을 경우를 대비해 Separate Chaining 방법으로 동작한다.  
이는 값과 값을 연결하는 방법으로 동작하는데(동일한 해시값을 가지면 동일한 버킷에 저장됨), 위와 같이 항상 동일한 hashCode를 반환하게 한다면 최악의 경우 수행시간이 O(N)으로 떨어진다.

### 해시값이 동일한 경우 내부적으로 구별하는 방법

```java
if (p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k))))
```

1. hashCode를 비교한다.
2. key의 주소가 같은지 비교한다.
3. equals로 논리적으로 동일한지 비교한다.

1번과 2번 조건을 동시에 만족하는 경우, 1번과 3번 조건을 동시에 만족하는 경우 중복키로 가정한다.

### 참고 자료

[equals와 hashCode는 왜 같이 재정의해야 할까?, 테코블](https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/)

[hashcode in Java, Baeldung](https://www.baeldung.com/java-hashcode)

[The Java HashMap Under the Hood, Baeldung](https://www.baeldung.com/java-hashmap-advanced)

Effective Java Item 11 - equals를 재정의하려거든 hashCode도 재정의하라