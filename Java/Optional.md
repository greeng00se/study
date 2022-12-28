### null 때문에 발생하는 문제

- 에러의 근원 NPE
- 복잡한 null check 코드
- null은 아무 의미도 없고, 형식도 없다.

### Optional

- null이 될 수 있는 객체를 감싸는 클래스
- `empty` → 빈 Optional 객체 반환
- `of` → null이 아닌 값을 포함하는 Optional 생성
- `ofNullalbe` → of와 달리 null값을 저장할 수 있는 Optional 생성

### 기본형으로 Optional을 사용하지 말아야하는 이유

- 스트림과 동일하게 기본형 특화된 클래스를 제공한다. ex) OptionalInt
- 성능을 향상 시킬 수 있지만 Optional 클래스가 제공하는 유용한 메서드를 지원하지 않는다.
- 다른 일반 Optional과 혼용 불가