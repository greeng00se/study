> Real MySQL 8.0을 읽고 정리한 글입니다.
> 

## MySQL 엔진의 잠금

- MySQL에서의 락은 스토리지 엔진 레벨과, MySQl 엔진 레벨로 나눌 수 있다.
- MySQL 엔진 레벨의 잠금은 모든 스토리지 엔진에 영향을 미친다.

## 글로벌 락(global lock)

- MySQL에서 제공하는 잠금 중 가장 넓은 범위를 가지고 있는 잠금이다.
    - 영향을 미치는 범위는 해당 서버 전체이다.
    - 작업 대상 테이블, 데이터베이스 상관 없이 동일하게 영향을 받는다.
- 한 세션에서 글로벌 락을 획득하면 해제 될 때 까지 조회를 제외한 대부분의 명령이 대기 상태가 된다.
- 데이터베이스에 존재하는 MyISAM이나 MEMORY 테이블에 대해 일관된 백업을 받아야할 때 사용한다.
- MySQL 8.0 부터 InnoDB 스토리지 엔진 기본 엔진으로 채택되면서 백업 시 조금 더 가벼운 백업 락을 사용한다.

```sql
-- GLOBAL LOCK
FLUSH TABLES WITH READ LOCK;
-- UNLOCK
UNLOCK TABLES;

-- BACKUP LOCK
LOCK INSTANCE FOR BACKUP;
-- UNLOCK
UNLOCK INSTANCE;
```

> 📌 MyISAM
> 
> - MySQL 5.5 버전 이전의 기본 스토리지 엔진이다.
> - 트랜잭션을 지원하지 않고, SELECT 작업 속도가 빠르다.

## 테이블 락(table lock)

- 개별 테이블 단위로 설정되는 잠금이다.
- 명시적 또는 묵시적으로 특정 테이블의 락을 획득할 수 있다.
- 묵시적 락은 MyISAM이나 MEMORY 테이블에 데이터를 변경하는 쿼리를 실행하면 발생한다.
    - innoDB는 DML 쿼리는 무시되고 DDL 일 경우에만 영향을 받는다.

```sql
-- TABLE LOCK
LOCK TABLES table_name [ READ | WRITE ]
-- UNLOCK
UNLOCK TABLES;
```

### 읽기 잠금(read lock)

- 데이터 조회를 위한 락, 공유 잠금(shared lock)으로도 불린다.
- 다른 트랜잭션에서 읽기가 가능하지만, 쓰기는 불가능하다.
- 데이터의 변경을 원한다면, 쓰기 잠금을 얻어야 한다.

### 쓰기 잠금(write lock)

- 데이터 변경을 위한 락, 배타적 잠금(exclusive lock)으로도 불린다.
- 락을 건 트랜잭션만이 해당 데이터에 접근 가능하다. 다른 트랜잭션의 경우 읽기, 쓰기가 불가능하다.

## 네임드 락(named lock)

- 임의의 문자열에 대한 잠금을 설정할 수 있는 잠금으로 유저 레벨 락으로도 불린다.
    - 단순히 사용자가 지정한 문자열에 대해 획득하고 반납하는 잠금이다.
- 배치 프로그램처럼 많은 레코드에 대해서 복잡한 요건으로 레코드를 변경하는 트랜잭션에 유용하게 사용된다.
    - 동일 데이터를 변경하거나 참조하는 프로그램끼리 분류해서 네임드 락을 걸고 쿼리를 실행해서 해결할 수 있다.
- MySQL 8.0 부터는 네임드락을 중첩해서 사용할 수 있고, 동시에 모두 해제하는 기능도 추가되었다.
    - 책에는 8.0부터라고 되어있지만 공식 문서를 찾아보니 5.7버전 이상부터 되는 것 같다.

```sql
-- SGVsbG8= 라는 문자열에 대한 잠금 획득, 이미 잠금을 사용중인 경우 1초 동안만 대기
SELECT GET_LOCK('SGVsbG8=', 1);

-- 문자열에 대한 잠금이 설정되어 있는지 확인한다.
SELECT IS_FREE_LOCK('SGVsbG8=');

-- 문자열에 대한 잠금을 해제한다.
SELECT RELEASE_LOCK('SGVsbG8=');

-- 위 3개 함수 모두 정상적으로 락을 획득하거나 해제한 경우에 1을, 아니면 0을 반환한다.

-- 모든 문자열에 대한 잠금을 해제한다. 해제된 잠금 수를 반환한다.
SELECT RELEASE_ALL_LOCKS();
```

## 메타데이터 락(metadata lock)

- 데이터베이스 객체의 이름이나 구조를 변경하는 경우 획득하는 잠금이다.
- 명시적으로 획득 또는 해제 할 수 없지만 테이블의 이름을 변경하는 경우 자동으로 획득한다.
- 보통 배치 프로그램에서 실시간으로 테이블을 바꿔야하는 경우에 사용된다.

```sql
-- 배치 프로그램에서 별도의 임시 테이블에 서비스용 랭킹 데이터 생성 후 기존 테이블을 백업하는 경우
-- 아래 구문 실행 시 메타데이터 락을 자동으로 획득한다.
RENAME TABLE rank TO rank_backup, rank_new TO rank;
```

## 참고 자료

- [MySQL의 User Level Lock를 활용한다면?, gywndi](https://gywn.net/2013/12/mysql-user-level-lock/)
- [Locking Functions, MySQL 5.7 Reference](https://dev.mysql.com/doc/refman/5.7/en/locking-functions.html#function_release-all-locks)
- [Locking Functions, MySQL 8.0 Reference](https://dev.mysql.com/doc/refman/8.0/en/locking-functions.html#function_release-all-locks)