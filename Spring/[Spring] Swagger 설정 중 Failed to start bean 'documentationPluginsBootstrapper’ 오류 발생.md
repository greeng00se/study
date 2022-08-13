### 개요

- Swagger 설정 중 Failed to start bean 'documentationPluginsBootstrapper’ 오류가 발생했습니다.
    - 사용하는 Swagger 버전은 2.9.2 버전입니다.
- 현재 스프링 버전 2.7.2를 사용하고 있는데 2.6버전부터 matching strategy의 기본값이 PathPatternParser로 변경되었다고 합니다.

### PathPattern Based Path Matching Strategy for Spring MVC

- The default strategy for matching request paths against registered Spring MVC handler mappings has changed from `AntPathMatcher` to `PathPatternParser`.
- The actuator endpoints now also use `PathPattern` based URL matching. Note that the path matching strategy for actuator endpoints is not configurable via a configuration property. If you are using Actuator and Springfox, this may result in your application failing to start. Please see [this Springfox issue for further details](https://github.com/springfox/springfox/issues/3462).

### 해결방법

```yaml
spring:
  mvc:
    pathmatch:
      matching-strategy: "ant_path_matcher"
```

- 기본 전략을`ant_path_matcher`로 변경하면 됩니다.

### 참고 자료

- [Spring Boot 2.6 Release Notes, spring-projects](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.6-Release-Notes)
- [Spring 5.3/Spring 2.4 support, springfox](https://github.com/springfox/springfox/issues/3462)