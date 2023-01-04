```groovy
plugins {
    id "org.jetbrains.kotlin.kapt" version "1.6.21"
}

dependencies {
    implementation 'com.querydsl:querydsl-jpa:5.0.0'
    kapt("com.querydsl:querydsl-apt:5.0.0:jpa")
    kapt("org.springframework.boot:spring-boot-configuration-processor")
}
```

### kapt(Kotlin Annotation Processing Tool)

- 코틀린에서 자바의 어노테이션을 처리할 때 코틀린 파일의 어노테이션 처리를 포함한다.
- JVM에서 Kotlin의 어노테이션을 포함시키기 위해 사용한다.