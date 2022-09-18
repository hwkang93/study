# spring-boot-starter-test 에서 junit-vintage-engine 이 exclude 되어있는 이유

Spring Initializer 로 프로젝트를 만들거나, 그 외에도 SpringBoot 프로젝트에서 테스트 라이브러리 spring-boot-starter-test 를 추가할 때
junit-vintage-engine 을 제외(exclude)한다.

```build.gradle```

```gradle
testImplementation('org.springframework.boot:spring-boot-starter-test') {
    exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
}
```

```pom.xml```

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>org.junit.vintage</groupId>
            <artifactId>junit-vintage-engine</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

여기서 junit-vintage-engine 을 제외하는데, 왜 굳이 제외하는지에 대해 알아보고자 한다.

### Junit5 의 구성

**Junit5** 는 JUnit Platform , JUnit Jupiter , JUnit Vintage 세 개의 모듈로 구성되어 있다.

- JUnit Platform
  - JVM 기반 테스트 프레임워크를 실행시키기 위한 모듈
  - JUnit4 환경에서도 플랫폼 모듈을 진행하는 테스트를 위해 ```Runner``` 를 제공한다.

- JUnit Jupiter
  - Junit5 에서 테스트 및 확장을 작성하기 위한 프로그래밍 모델 + 확장 모델이다.

- JUnit Vintage
  - 이전 Junit 모델(JUnit4, JUnit3)에서 작성된 테스트 코드를 JUnit Platform 에서 실행시키기 위한 모듈이다.


### JUnit 테스트 진행 흐름

JUnit3, JUnit4 으로 작성된 코드 -> Junit Vintage -> JUnit Platform
JUnit5 으로 작성된 코드 -> JUnit Jupiter -> JUnit Platform

### junit-vintage-engine 을 제외하는 이유

- 위에서 설명한 것과 같이 junit-vintage-engine 은 JUnit5 이전 모델에서 작성한 테스트코드를 실행시키기 위해 존재한다.
- JUnit5 만으로 작성된 테스트코드에서 굳이 junit-vintage-engine 은 필요하지 않다.
- 불필요한 모듈이라고 판단하여 제외한 것이다.
- 만약 JUnit3, JUnit4 를 사용해서 테스트코드를 작성했다면, exclude 를 제거한다.




## Reference

https://rutgo-letsgo.tistory.com/94