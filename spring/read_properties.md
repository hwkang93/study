# java 파일에서 properties 파일 내 선언된 값 세팅하기

Properties 파일에 정의된 값을 소스코드에서 사용해야 할 일이 생긴다. 
이러한 경우를 대비해, Properties 파일에 정의된 값들을 클래스파일에 저장하고 조회한다.


## application.properties

Spring Framework 에서 제공하는 어노테이션인 ```org.springframework.beans.factory.annotation.Value``` 을 사용하여, 값을 세팅할 수 있다.
```application*.properties``` 의 명명규칙이 적용된 properties 파일에서만 적용이 가능하며, 클래스 파일은 Spring Bean 으로 등록되어 있어야 한다.

```application-dev.properties```

```properties
user.name = 'kang'
user.age = 30
```

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class UserProperties {
    @Value("${user.name}")
    private String userName;
    
    @Value("${user.age}")
    private int userAge;
}

```

## @PropertySource

```org.springframework.context.annotation``` 패키지의 PropertySource 어노테이션을 사용해 properties 파일을 불러올 수도 있다.   
사용 방법 및 특징은 위의 application.properties 방법과 유사하다.

```java
@Component
@PropertySource(ignoreResourceNotFound = true, value = {"classpath:user.properties", "file:/config/user.properties"})
public class UserProperties {
    @Value("${user.name}")
    private String userName;

    @Value("${user.age}")
    private int userAge;
}
```