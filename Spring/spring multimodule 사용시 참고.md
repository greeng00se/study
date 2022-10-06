# spring multimodule 사용시 참고

### settings.gradle

- include ‘모듈명’ 필요
- 이걸 이용해서 의존성 설정을 추가할 수 있음

### 의존성 설정

```
// root project -> settings.gradle에 선언한 값을 참고함
implementation project(':module-common')
```

### 빈 컴포넌트 스캔

- 애플리케이션 위치 변경.. X
- SpringBootApplication scanBasePackages 사용

```groovy
@SpringBootApplication(
        scanBasePackages = {"com.example.moduleapi", "com.example.modulecommon"}
)
public class ModuleApiApplication {

    public static void main(String[] args) {
        SpringApplication.run(ModuleApiApplication.class, args);
    }

}
```

### Entity와 Repository

- 마찬가지로 스캔 설정을 해줘야 한다.
- EntityScan, EnableJpaRepositories 사용

```groovy
@SpringBootApplication(
        scanBasePackages = {"com.example.moduleapi", "com.example.modulecommon"}
)
@EntityScan("com.example.modulecommon.domain")
@EnableJpaRepositories("com.example.modulecommon.repository")
public class ModuleApiApplication {

    public static void main(String[] args) {
        SpringApplication.run(ModuleApiApplication.class, args);
    }

}
```

### gradle build

```groovy
// jar file 생성하지 않도록 설정
tasks.bootJar {
    enabled = false
}

// 기본값은 true xxx-plain.jar 생성 -> plain은 dependency 포함하지 않음
tasks.jar {
    enabled = true
}
```

- application이 실행되는 모듈 제외하고 위와 같이 설정
- 빌드
    - `./gradlew clean :module-api:buildNeeded --stacktrace --info --refresh-dependencies -x test`