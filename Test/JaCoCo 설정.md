### JaCoCo(Java Code Coverage)

- 자바용 무료 코드 커버리지 라이브러리입니다.
- Gradle을 사용하는 경우 Jacoco 플러그인을 통해 프로젝트에 적용할 수 있습니다.

### JaCoCo 플러그인 적용

```groovy
plugins {
    id 'jacoco'
}
```

- plugins에 jacoco를 추가합니다.
- 플러그인을 적용한다면 `jacocoTestReport`, `jacocoTestCoverageVerification` 작업이 기본적으로 생성됩니다.

### 테스트와 보고서간 종속성 설정

```groovy
test{
    useJUnitPlatform()
    finalizedBy 'jacocoTestReport'
}
```

- 보고서 생성의 경우 테스트가 선행되어야합니다. 따라서 Test를 실행한다면 보고서를 생성할 수 있게 설정합니다.
- HTML로 생성된 보고서는 다음 위치에 생성됩니다.
    - `$buildDir/reports/jacoco/test`

### JaCoCo 리포트에서 제외

```groovy
private excludedClassFilesForReport(classDirectories) {
    classDirectories.setFrom(
            files(classDirectories.files.collect {
                fileTree(dir: it, exclude: [
                        "**/Q*",
                        "**/*Dto*",
                        "**/*Application*",
                        "**/*Config*"
                ])
            })
    )
}
```

- `jacocoTestReport`, `jacocoTestCoverageVerification` 작업에서 수집을 원하지 않는 클래스 파일을 제외하기 위해 사용합니다.
- `jacocoTestCoverageVerification` rule의 exclude에서 제외할 클래스 파일명을 추가하는 방법도 있지만 전체 커버리지에 대한 룰이 클래스 파일 제외 전 기준으로 적용이 되었습니다.
- 따라서 위 메서드를 작업당 한 번씩 호출하는 방법으로 리포트에서 제외시켰습니다.

### jacocoTestReport 설정

```groovy
jacocoTestReport {
    // 생성할 보고서 유형 설정
    reports {
        html.enabled true
        xml.enabled false
        csv.enabled true
    }

    excludedClassFilesForReport(classDirectories)

    finalizedBy 'jacocoTestCoverageVerification'
}
```

- 보고서 생성에 대한 task입니다.
- `reports`를 통해 생성할 보고서의 유형을 선택할 수 있습니다.
- 보고서를 생성하고 나면 `jacocoTestCoverageVerification`를 실행하도록 합니다.

### jacocoTestCoverageVerification 설정

```groovy
jacocoTestCoverageVerification {
    excludedClassFilesForReport(classDirectories)
    violationRules {

        rule {
            // element default = BUNDLE
            enabled = true

            limit {
                // counter default = INSTRUCTION
                // value default = COVEREDRATIO
                minimum = 0.80
            }
        }
    }
}
```

- 커버리지 기준을 만족하는지에 대해 검증을 하는 task입니다.
- `violationRules`로 커버리지 기준을 설정할 수 있습니다.
- rule은 여러 개 설정할 수 있고 브랜치 커버리지, 라인 커버리지 등 다양하게 기준을 설정할 수 있습니다.
    - element → 커버리지 체크 기준을 설정할 수 있습니다.
    - enabled → 룰의 적용 여부를 설정할 수 있습니다.
    - limit → 제한 설정에 대한 내용이 포함됩니다.

### Lombok jacoco에서 제외

```bash
lombok.addLombokGeneratedAnnotation = true
```

- 프로젝트 최상단에 lombok.config 파일을 생성해 위와 같이 작성합니다.
- lombok으로 자동 생성된 메서드는 jacoco에서 제외됩니다.

### 참고 자료

- [jacoco.org](https://www.jacoco.org/jacoco/)
- [jacoco plugin, gradle](https://docs.gradle.org/current/userguide/jacoco_plugin.html#header)
- [https://techblog.woowahan.com/2661/](https://techblog.woowahan.com/2661/)
- [Exclusions from Jacoco Report, Baeldung](https://www.baeldung.com/jacoco-report-exclude)
- [https://stackoverflow.com/questions/29887805/filter-jacoco-coverage-reports-with-gradle](https://stackoverflow.com/questions/29887805/filter-jacoco-coverage-reports-with-gradle)
- [JacocoViolationRule, gradle](https://docs.gradle.org/current/javadoc/org/gradle/testing/jacoco/tasks/rules/JacocoViolationRule.html)