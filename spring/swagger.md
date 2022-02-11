
## 예제 만들면서 발생한 오류

### org.springframework.context.ApplicationContextException: Failed to start bean 'documentationPluginsBootstrapper

- SpringBoot 와 Swagger 간의 버전 충돌이 원인
- **SpringBoot 의 버전을 2.6.3 -> 2.4.0 으로 다운그레이드 후 해결**

```
plugins {
	/*id 'org.springframework.boot' version '2.6.3'*/
	id 'org.springframework.boot' version '2.4.0'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}
```

### java: java.lang.IllegalAccessError: class lombok.javac.apt.LombokProcessor

- jdk 컴파일러가 롬복 모듈에 접근할 수 없다는 오류
- **롬복 라이브러리 버전을 명시하는 방법으로 해결**

```build.gradle``` 에서 롬복 버전을 1.18.22 로 명시
```
compileOnly 'org.projectlombok:lombok:1.18.22'
annotationProcessor 'org.projectlombok:lombok:1.18.22'
```


## Reference

[스프링부트 Swagger UI 적용 방법 - 스프링부트 2.2 미만 (Spring Boot Swagger UI)](https://nahwasa.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-Swagger-UI-%EC%A0%81%EC%9A%A9-%EB%B0%A9%EB%B2%95-Spring-Boot-Swagger-UI)