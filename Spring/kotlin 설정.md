```groovy
plugins {
    id 'org.jetbrains.kotlin.jvm' version '1.7.21'
    id 'org.jetbrains.kotlin.plugin.jpa' version '1.7.21'
    id 'org.jetbrains.kotlin.plugin.spring' version '1.7.21'
    id "org.jetbrains.kotlin.kapt" version '1.7.21'
}

allOpen {
    // Spring Boot 2.X.X
    annotation 'javax.persistence.Entity'
    annotation 'javax.persistence.MappedSuperclass'
    annotation 'javax.persistence.Embeddable'

    // < Spring Boot 3.0.0
    annotation 'jakarta.persistence.Entity'
    annotation 'jakarta.persistence.MappedSuperclass'
    annotation 'jakarta.persistence.Embeddable'
}

dependencies {
    implementation 'org.jetbrains.kotlin:kotlin-stdlib-jdk8'
    implementation 'org.jetbrains.kotlin:kotlin-reflect:1.7.21'
    implementation 'com.fasterxml.jackson.module:jackson-module-kotlin:2.13.3'

    // Querydsl
    implementation 'com.querydsl:querydsl-jpa:5.0.0'
    kapt 'com.querydsl:querydsl-apt:5.0.0:jpa'
    kapt 'org.springframework.boot:spring-boot-configuration-processor'
}
```

### kapt(Kotlin Annotation Processing Tool)

- 코틀린에서 자바의 어노테이션을 처리할 때 코틀린 파일의 어노테이션 처리를 포함한다.
- JVM에서 Kotlin의 어노테이션을 포함시키기 위해 사용한다.
  