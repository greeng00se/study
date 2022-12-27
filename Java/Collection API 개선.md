컬렉션 API 개선

### 컬렉션 팩토리

- `Arrays.asList` → 요소 추가 및 삭제 X, 요소 갱신 O
- `List.of` → 리스트 팩토리
    - 요소 추가, 삭제, 갱신 X
    - 열개 이상의 요소를 가진 리스트를 만들 땐 가변 인수를 사용
- `Set.of` → 집합 팩토리
- `Map.of` → 맵 팩토리

### 리스트와 집합 처리

- 자바 8에서는 `List`, `Set` 인터페이스에 다음 메서드가 추가되었다.
- `removeIf` → `Predicate`를 만족하는 요소를 제거
- `replaceAll` → `UnaryOperator` 함수를 이용해 요소를 변경
- `sort` → `List` 인터페이스에서 제공, 리스트 정렬

### 맵 처리

- `forEach` → `BiConsumer`를 인수로 받아 반복
- `Entry.comparingByValue`, `Entry.comparingByKey` → 맵의 항목을 값 또는 키를 기준으로 정렬
- `getOrDefault` → 키가 존재하지 않으면 기본값 반환
- 계산 패턴
    - `computeIfAbsent` → 제공된 키에 해당값이 없으면, 키를 이용해 새 값을 계산하고 맵에 추가
    - `computeIfPresent` → 제공된 키가 존재하면 새 값을 계산하고 맵에 추가
    - `compute` → 제공된 키로 새 값을 계산하고 맵에 저장
- 교체 패턴
    - `replaceAll` -> `BiFunction`을 적용한 결과로 각 항목의 값을 교체
    - `Replace` → 키가 존재하면 맵의 값을 변경
- `putAll` → 맵 합침, 키 충돌의 경우 `merge` 사용 권장