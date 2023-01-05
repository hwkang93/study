# Spring Framework 3.0.x 버전에서의 Security 주의사항  

start.spring.io 에서 Spring Boot 프로젝트를 생성하면 ```spring-boot-starter-parent``` 의 버전이 ```3.0.2``` 버전으로 생성된다.

ex) 2023-02-01 기준

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.0.2</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

**개발을 진행하다 보면 Spring Framework 에서 참조하는 자바가 javax 가 아닌 jakarta 로 변경된 것으로 보인다.**

jakarta 가 아닌 javax 로 프로젝트를 진행해야 한다면 ```spring-boot-starter-parent``` 의 버전을 2.7.x 로 낮춰주면 된다.