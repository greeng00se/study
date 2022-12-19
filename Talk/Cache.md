### 캐시(Cache)

- 데이터나 값을 미리 복사해 놓는 임시 장소

### 캐싱

- 자주 사용하는 데이터를 저장해서 재활용하는 기술

## 스프링 캐싱

### 캐시 추상화(Cache Abstraction)

- 스프링 빈의 메서드에 캐시를 적용할 수 있는 기능 제공
- AOP를 이용해 메서드 내부 구현에 영향을 미치지 않고 적용
- 특정 캐시 기술에 종속적이지 않고, Redis와 같은 스토어에 의존하지 않아도 된다.

### 캐시 매니저(Cache Manager)

- 캐시 추상화에서는 캐시 기술을 지원하는 캐시 매니저를 빈으로 등록해야한다.
- `ConcurrentMapCacheManager`
    - 캐시 정보 Map 타입
    - 빠르고 별다른 설정이 필요없다.
- `SimpleCacheManager`
    - 사용할 캐시를 직접 등록하여 사용하기 위한 캐시 매니저
- `EhCacheCacheManager`
    - EhCache를 지원하는 캐시 매니저

### 캐시를 사용할 때의 주의 점

- 반복적으로 동일한 결과를 반환할 때 용이하다.
- 매번 다른 결과를 반환하는 경우 성능 저하가 일어나고, 저장 및 확인 작업에서 부하가 생긴다.

### 키워드

@Cacheable, @CacheEvict, @CachePut

### 참고 자료

- [https://www.youtube.com/watch?v=H4J-8pPMvEU&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=34](https://www.youtube.com/watch?v=H4J-8pPMvEU&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=34)
- [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/concurrent/ConcurrentMapCacheManager.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/concurrent/ConcurrentMapCacheManager.html)
- [https://www.baeldung.com/spring-multiple-cache-managers](https://www.baeldung.com/spring-multiple-cache-managers)