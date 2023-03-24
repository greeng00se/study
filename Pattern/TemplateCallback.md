체스 미션에는 4단계에서 DAO를 사용합니다.

JDBC를 사용할 때 데이터베이스의 커넥션을 얻고, try-with-resource를 사용하는 부분이 반복됩니다.

일단 아래의 insert와 delete의 중복을 제거해봅시다.

```java
public void insert(final User user) {
    final String sql = "INSERT INTO User (user_id, name) VALUES (?, ?)";
    try (final var connection = getConnection();
         final var preparedStatement = connection.prepareStatement(query)) {
        preparedStatement.setString(1, user.getUserId());
        preparedStatement.setString(2, user.getName());
        preparedStatement.executeUpdate();
    } catch (final SQLException e) {
        throw new RuntimeException(e);
    }
}

public void delete(final String userId) {
    final var query = "DELETE FROM user WHERE user_id = ?";
    try (final var connection = getConnection();
         final var preparedStatement = connection.prepareStatement(query)) {
        preparedStatement.setString(1, userId);
        preparedStatement.executeUpdate();
    } catch (final SQLException e) {
        throw new RuntimeException(e);
    }
}
```

### 변하지 않는 부분과 변하는 부분 분리하기

변하지 않는 부분: try-with-resource 사용, query를 사용하는 부분, 매개변수를 사용하는 부분, `executeUpdate`로 실행 등등

변하는 부분: query문, 매개변수 + 매개변수의 갯수

insert와 delete가 둘 다 쿼리를 받는 부분은 중복을 쉽게 제거할 수 있을 것 같은데, 매개변수는 어떻게 중복을 제거할 수 있을까요?

다음과 같이 가변인수를 사용한다면 쉽게 중복을 제거할 수 있습니다.

```java
public void insert(final User user) {
    final String query = "INSERT INTO User (user_id, name) VALUES (?, ?)";
    executeUpdate(query, user.getUserId(), user.getName());
}

public void delete(final String userId) {
    final var query = "DELETE FROM user WHERE user_id = ?";
    executeUpdate(userId, query);
}

private void executeUpdate(final String query, final String... parameters) {
    try (final var connection = getConnection();
         final var preparedStatement = connection.prepareStatement(query)) {
        for (int i = 1; i <= parameters.length; i++) {
            preparedStatement.setString(1, parameters[i - 1]);
        }
        preparedStatement.executeUpdate();
    } catch (final SQLException e) {
        throw new IllegalArgumentException(e.getMessage());
    }
}
```

### 조회 분리하기

그렇다면 조회의 경우 어떻게 분리할 수 있을까요?

아래의 코드를 봐도 위에서 적용한 방법으로는 분리할 수 없을 것 같아 보입니다.

```java
public List<User> findAll() {
    final var query = "SELECT * FROM user";
    try (final var connection = getConnection();
         final var preparedStatement = connection.prepareStatement(query)) {
        final var resultSet = preparedStatement.executeQuery();
        final List<User> result = new ArrayList<>();
        while (resultSet.next()) {
            result.add(new User(
                    resultSet.getString("user_id"),
                    resultSet.getString("name")
            ));
        }
        return result;
    } catch (final SQLException e) {
        throw new IllegalArgumentException(e.getMessage());
    }
}

public User findByUserId(final String userId) {
    final var query = "SELECT * FROM user WHERE user_id = ?";
    try (final var connection = getConnection();
         final var preparedStatement = connection.prepareStatement(query)) {
        preparedStatement.setString(1, userId);

        final var resultSet = preparedStatement.executeQuery();
        if (resultSet.next()) {
            return new User(
                    resultSet.getString("user_id"),
                    resultSet.getString("name")
            );
        }
    } catch (final SQLException e) {
        throw new IllegalArgumentException(e.getMessage());
    }
    return null;
}
```

그럼 어떤 방법을 사용해야 할까요?

### 콜백

프로그래밍에서 콜백은 다른 코드의 인수로 넘겨주는 실행 가능한 코드를 말합니다.

자바에서는 람다나 익명 클래스를 넘겨서 사용할 수 있습니다.

![image](https://user-images.githubusercontent.com/58586537/227392528-8139e442-45f7-451b-80e4-845986c791b1.png)

이렇게 사용하는 것을 템플릿 콜백이라 하는데, 복잡한 설명보다 코드로 직접 작성해보겠습니다.

### 분리할 부분 생각하기

데이터베이스에서 값을 조회하고, 해당 값을 객체로 매핑하여 값을 반환해야합니다.

또한 String 뿐만이 아닌 다양한 타입의 값을 반환해야 합니다.

executeQuery로 조회한 값은 ResultSet 안에 들어가있고, 이를 우리가 원하는 타입의 값으로 변환해야하니 일단 인터페이스를 하나 만들어볼게요.

### RowMapper 인터페이스

하나의 메서드 시그니처를 가지는 인터페이스를 정의해보겠습니다.

전체 조회를 하는 부분을 위해 ResultSet을 받아, List<User> 값을 반환하는 메서드 시그니처가 존재하는 인터페이스를 구현하여 사용해보겠습니다.

```java
@FunctionalInterface
public interface RowMapper {
    List<User> mapRow(final ResultSet resultSet) throws SQLException;
}
```

### 조회 분리하기

위에 예제코드 findAll에서 값을 매핑하는 부분만 분리를 하고 메서드에서 RowMapper<List<User>> 타입을 받도록 했습니다.

```java
private List<User> executeQuery(final String query, final RowMapper rowMapper) {
      try (final var connection = getConnection();
         final var preparedStatement = connection.prepareStatement(query)) {
        final var resultSet = preparedStatement.executeQuery();
        return rowMapper.mapRow(resultSet);
    } catch (final SQLException e) {
        throw new IllegalArgumentException(e.getMessage());
    }
}
```

따라서 호출하는 쪽에서는 다음과 같이 작성할 수 있습니다.

```java
public List<User> findAll() {
    final var query = "SELECT * FROM user";
    return executeQuery(query, new RowMapper() {
        @Override
        public List<User> mapRow(final ResultSet resultSet) throws SQLException {
            final List<User> result = new ArrayList<>();
            while (resultSet.next()) {
                result.add(new User(
                        resultSet.getString("user_id"),
                        resultSet.getString("name")
                ));
            }
            return result;
        }
    });
}
```

이를 IntelliJ의 도움을 받아 람다로 변환할 수 있습니다.

```java
public List<User> findAll() {
    final var query = "SELECT * FROM user";
    return executeQuery(query, resultSet -> {
        final List<User> result = new ArrayList<>();
        while (resultSet.next()) {
            result.add(new User(
                    resultSet.getString("user_id"),
                    resultSet.getString("name")
            ));
        }
        return result;
    });
}
```

하지만 현재의 코드만으로는 다양한 타입이나 매개변수를 받을 수 없을 것 같네요.

제네릭을 사용하여 다양한 타입을 반환할 수 있게 인터페이스의 메서드 시그니처를 변경해보겠습니다.

```java
@FunctionalInterface
public interface RowMapper<T> {
    T mapRow(final ResultSet resultSet) throws SQLException;
}
```

위에서 사용한 가변인수를 사용하여 매개변수도 여러 개 받을 수 있게 적용한다면 다음과 같이 변경할 수 있을 것 같습니다.

```java
private <T> T executeQuery(final String query, final RowMapper<T> rowMapper, final String... parameters) {
    try (final var connection = getConnection();
         final var preparedStatement = connection.prepareStatement(query)) {
        final var resultSet = preparedStatement.executeQuery();
        for (int i = 1; i <= parameters.length; i++) {
            preparedStatement.setString(1, parameters[i - 1]);
        }
        return rowMapper.mapRow(resultSet);
    } catch (final SQLException e) {
        throw new IllegalArgumentException(e.getMessage());
    }
}
```

findByUserId도 다음과 같이 적용할 수 있을 것 같습니다.

```java
public User findByUserId(final String userId) {
    final var query = "SELECT * FROM user WHERE user_id = ?";
    return executeQuery(query, resultSet -> {
        if (resultSet.next()) {
            return new User(
                    resultSet.getString("user_id"),
                    resultSet.getString("name")
            );
        }
        return null;
    }, userId);
}
```

### 템플릿 분리하기

executeUpdate 메서드와 executeQuery 메서드를 클래스로 분리해보겠습니다.

저는 하나의 Connection을 받아 커넥션을 종료하지 않고 계속 사용하기 위해 필드로 받아놨습니다.

Connection을 매번 생성하려면 커넥션을 만들어주는 클래스를 필드로 가지는 방법도 있을 것 같습니다.

```java
public class JdbcTemplate {

    private final Connection connection;

    public JdbcTemplate(final Connection connection) {
        this.connection = connection;
    }

    private void executeUpdate(final String query, final String... parameters) {
        try (final var preparedStatement = connection.prepareStatement(query)) {
            for (int i = 1; i <= parameters.length; i++) {
                preparedStatement.setString(1, parameters[i - 1]);
            }
            preparedStatement.executeUpdate();
        } catch (final SQLException e) {
            throw new IllegalArgumentException(e.getMessage());
        }
    }

    private <T> T executeQuery(final String query, final RowMapper<T> rowMapper, final String... parameters) {
        try (final var preparedStatement = connection.prepareStatement(query)) {
            final var resultSet = preparedStatement.executeQuery();
            for (int i = 1; i <= parameters.length; i++) {
                preparedStatement.setString(1, parameters[i - 1]);
            }
            return rowMapper.mapRow(resultSet);
        } catch (final SQLException e) {
            throw new IllegalArgumentException(e.getMessage());
        }
    }
}
```

### DAO

DAO에서는 위에서 분리한 JdbcTemplate을 생성자로 주입받아 사용할 수 있을 것 같습니다!

```java
public class MyDao {

    private final JdbcTemplate jdbcTemplate;

    public MyDao(final JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public void insert(final User user) {
        final String query = "INSERT INTO User (user_id, name) VALUES (?, ?)";
        jdbcTemplate.executeUpdate(query, user.getUserId(), user.getName());
    }

    public void delete(final String userId) {
        final var query = "DELETE FROM user WHERE user_id = ?";
        jdbcTemplate.executeUpdate(userId, query);
    }

    public List<User> findAll() {
        final var query = "SELECT * FROM user";
        return jdbcTemplate.executeQuery(query, resultSet -> {
            final List<User> result = new ArrayList<>();
            while (resultSet.next()) {
                result.add(new User(
                        resultSet.getString("user_id"),
                        resultSet.getString("name")
                ));
            }
            return result;
        });
    }

    public User findByUserId(final String userId) {
        final var query = "SELECT * FROM user WHERE user_id = ?";
        return jdbcTemplate.executeQuery(query, resultSet -> {
            if (resultSet.next()) {
                return new User(
                        resultSet.getString("user_id"),
                        resultSet.getString("name")
                );
            }
            return null;
        }, userId);
    }
}
```

### 마무리

위에서 적용한 패턴은 템플릿 콜백 패턴입니다.

이는 전략 패턴의 변형으로, 전략 패턴을 익명 클래스와 함께 사용하는 패턴입니다.
