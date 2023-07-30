# Spring AOP

## AOP 란?

AOP 는 Aspect Oriented Programming 의 약자로 **관점 지향 프로그래밍** 을 의미한다.

어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어서 보고 그 관점을 기준으로 각각 모듈화하겠다는 것이다.

기능을 구현할 때 메인 비즈니스 로직이 있고, 그 외적인 로깅, 트랜잭션, 보안 등의 공통된 관심사들이 존재한다. 
AOP 는 이러한 로직들을 따로 정리하고 모듈화하여 관리하고자 하는 목적을 가지고 탄생했다.


## AOP 용어

다음은 AOP 에서 사용하는 주요 용어들이다.

### Aspect

횡단 관심사(Crosscutting Concerns) 를 모듈화한 것


### Target Object

횡단 기능이 적용될 핵심 비즈니스 로직이 작성된 객체

Spring AOP 에서는 실제 객체 대신에 Proxy 객체를 사용하여 구현되기 때문에, Target Object 는 항상 Proxy Object 이다.


### Advice

횡단 기능 구현 코드


### JoinPoint

Advice 가 적용될 위치

Spring AOP 에서 JoinPoint 는 항상 메소드 실행을 바라보기 때문에 JoinPoint 정보는 메소드 정보이며, 
Advice 에서 메소드 정보를 사용할 수 있다.

### Pointcut

JoinPoint 의 상세한 스펙을 정의한 것. 
어떤 Advice(횡단 기능 구현 코드)가 어느 JoinPoint(코드 실행 시점)에서 실행될 지 정의한 것.


### AOP Proxy

Aspect 를 대신 수행하기 위해 AOP 프레임워크에 생성된 객체이다. 
Spring AOP 에서는 ```java.lang.reflet.Proxy``` 객체를 사용하여 Proxy 객체를 동적으로 생성

> Proxy : 어떤 일을 대신 하는 것을 의미함.


### Weaving

특정 JoinPoint 에 Advice 하여 핵심 기능과 횡단 기능이 교차하여 새롭게 생성된 객체를 프로세스에 적용하는 일련의 모든 과정.

비즈니스 로직 코드에 횡단 기능 구현 코드를 JoinPoint 에서 정의한 위치에 덧붙여 완성된 코드(비즈니스로직 + 부가기능)를 프로세스에 적용하는 과정

수행 시점에 따라 3가지로 분류한다.

- CTW : Compile-Time Weaving
- LTW : Load-Time Weaving
- RTW : Run-Time Weaving

Spring AOP 에서는 RTW 를 채택하여 사용하고 있다.


## Spring AOP

Spring AOP 는 AOP 의 모든 기능을 제공하기 위해 만들어진 것이 아니라, 스프링 IoC 와 연동하여 어플리케이션에서 가장 흔하게 발생하는 문제
(중복 코드, 프록시 클래스 작성의 번거로움, 객체들 간 복잡도 증가 등)에 대한 해결책을 지원하는 것을 목적으로 만들어졌다.

Spring AOP 는 프록시를 기반으로 구현되어 있으며, Spring IoC 컨테이너와 함께 사용된다. 
Aspect 는 Spring Bean 으로 등록되어 IoC 컨테이너에서 관리된다.

Spring AOP 에서 PointCut 을 정의할 때는 AspectJ pointcut 표현식 문법을 사용한다.

> **Pointcut 표현식 문법**
> 
> ```execution([접근제한자(생략가능)] [리턴타입] [패키지 경로].[클래스 명].[메소드명].([파라미터|..(모든 파라미터)]))```
> 
> 예시)
> 
> ```execution( * hwkang.study.springaop.user.service.UserServiceImpl.*(..))```
> 
> 설명)
> 
> (접근제한자는 생략)
> 
> ```*``` : 모든 리턴타입 허용
> 
> ```hwkang.study.springaop.user.service``` : 서비스 경로
> 
> ```UserServiceImpl``` : 클래스 명
> 
> ```*``` : 모든 메소드 허용
> 
> ```(..)``` 모든 파라미터 허용
> 
> **hwkang.study.springaop.user.service 패키지의 UserServiceImpl 클래스 내의 모든 메소드(리턴타입, 파라미터 상관 없이)에 대해 PointCut 으로 설정**

### Spring AOP 특징
- (AspectJ 와 비교) 비교적 사용하기에 편리하다.
- 프록시 기반 AOP 이므로, 기본적으로 메소드에만 JoinPoint 가 적용 가능하다.
- 동일한 클래스 내에서 다른 메소드를 호출할 때에는 Aspect 가 적용되지 않는다.
- Spring Bean 객체 혹은 IoC 컨테이너에서 관리하지 않는 객체의 경우 Aspect 가 적용되지 않는다.
- (AspectJ 와 비교) 오버헤드가 있을 수 있다.
- Runtime Weaving 을 채택하여 사용하고 있다.


## Spring 에서 AOP 를 구현하는 예제

다음은 개발 환경이다.

- Springboot Version : 2.6.2
- JAVA Version : 11
- Gradle Project
- IntelliJ

먼저 ```build.gradle``` 파일을 열고 Spring AOP 라이브러리를 추가한다.

```java
//Spring AOP
implementation 'org.springframework.boot:spring-boot-starter-aop'
```

설정 정보가 적용이 되면 ```@SpringBootApplication``` 이 붙은 최상위 어플리케이션 클래스로 이동하여,
```@EnableAspectJAutoProxy``` 어노테이션을 추가해준다.

```java
@SpringBootApplication
@EnableAspectJAutoProxy
public class SpringAopApplication {
    public static void main(String[] args) {
        SpringAopApplication.run(SpringAopApplication.class, args);
    }
}
```

그럼 Spring AOP 를 사용할 준비가 끝났다.

이제 Spring AOP 에서 제공하는 어노테이션으로 예제 소스코드와 함께 알아보자.

### 테스트를 위한 서비스 만들기

AOP 테스트를 위해 간단한 테스트 및 서비스를 만들어보겠다.

- **AopTest.java**

```java
package hwkang.study.springaop;

@SpringBootTest
public class AopTest {

    @Autowired
    UserService userService;

    @Test
    @DisplayName("사용자 아이디가 1인 사용자의 이름은 khw 이다")
    void findUser() {
        final long userId = 1L;

        UserDto userDto = userService.findByUserId(userId);

        assertThat(userDto.getUserName()).isEqualTo("khw");
    }
}
```

테스트를 성공시키기 위한 클래스들이 필요하다.
```UserService.java``` , ```UserServiceImpl.java``` , ```UserDto.java``` 세 개의 클래스를 만들어준다.

> 롬복 라이브러리를 사용해 소스코드를 간소화했다.

- **UserDto.java**

```java
package hwkang.study.springaop.user.data;

@Getter
@AllArgsConstructor
public class UserDto {
    
    //사용자 ID
    private long userId;
    
    //사용자 명
    private String userName;
}
```

- **UserService.java**

```java
package hwkang.study.springaop.user.service;

public interface UserService {

    /**
     * 사용자 아이디를 입력받아 결과 사용자 객체를 리턴한다.
     * 
     * @param userId
     * @return UserDto
     */
    UserDto findByUserId(long userId);
    
}
```

- **UserServiceImpl.java**

```java
package hwkang.study.springaop.user.service;

@Service
public class UserServiceImpl implements UserService {

    final List<UserDto> userList = Arrays.asList(
            new UserDto(1, "khw"),
            new UserDto(2, "chr"),
            new UserDto(3, "kjh")
    );

    @Override
    public UserDto findByUserId(long userId) {
        return userList.stream()
                .filter(userDto -> userDto.getUserId() == userId)
                .findFirst()
                .orElseThrow(() -> new NullPointerException());
    }
}
```




### 메소드 실행 전 메소드 정보 로그로 출력하기

메소드 실행 전에 메소드 명을 출력하는 간단한 AOP 기능을 만들어보겠다.

Aop 설정 파일을 만들어준다.
메소드 정보를 출력하는 설정 파일이기 때문에 ```MethodInfoAopConfig``` 라는 클래스 명으로 만들었다.

```java
@Aspect
@Configuration
@Slf4j
public class MethodInfoAopConfig {

    @Before("execution( * hwkang.study.springaop.user.service.UserServiceImpl.*(..))")
    void before(JoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();

        log.info(" [ UserServiceImpl." + methodName + " ] start");
    }
}
```

```@Before``` 어노테이션을 사용하면 PointCut 이전에 특정 기능을 수행할 수 있다.
위의 소스코드는 패키지 경로.UserServiceImpl 클래스 내의 모든 메소드(파라미터 타입, 리턴 타입 상관 없이) 실행 전에
```before()``` 를 실행해 메소드 명을 로그로 표출하도록 명시하고 있다.


위의 테스트를 실행시켜 보면 원하는 결과가 정상적으로 호출됨을 알 수 있다.

```
2022-01-28 09:57:00.922  INFO 1180 --- [    Test worker] hwkang.study.springaop.AopTest           : Starting AopTest using Java 17.0.2 on DESKTOP-V1ME5NQ with PID 1180 (started by KHW-IPC in C:\Users\KHW-IPC\IdeaProjects\spring-aop)
2022-01-28 09:57:00.923  INFO 1180 --- [    Test worker] hwkang.study.springaop.AopTest           : No active profile set, falling back to default profiles: default
2022-01-28 09:57:02.931  INFO 1180 --- [    Test worker] hwkang.study.springaop.AopTest           : Started AopTest in 2.299 seconds (JVM running for 3.584)
2022-01-28 09:57:03.400  INFO 1180 --- [    Test worker] h.s.s.config.aop.MethodInfoAopConfig     :  [ UserServiceImpl.findByUserId ] start
```

### 메소드 실행 후 에러가 발생했을 시 로그 쌓기 

이번엔 메소드 실행 후 에러가 발생했을 경우 로그를 쌓는 기능을 추가했다.

```java
@Aspect
@Configuration
@Slf4j
public class MethodInfoAopConfig {

    //...

    @AfterThrowing(
            pointcut = "execution( * hwkang.study.springaop.user.service.UserServiceImpl.*(..))",
            throwing = "exception"
    )
    void afterThrowing(JoinPoint joinPoint, Exception exception) {
        String methodName = joinPoint.getSignature().getName();

        if(exception instanceof NullPointerException) {
            log.error(" [ " + methodName + " ] NullPointerException occurred.");
        }
        else {
            log.error(" [ " + methodName + " ] etc Exception occurred.", exception.getMessage());
        }
    }
}
```

```@AfterThrowing``` 어노테이션을 이용해 메소드에서 예외를 던지면 해당 AOP 기능을 수행한다. ```@Before``` 과 마찬가지고 Pointcut 을 명시하고
추가로 예외 객체를 담을 ```throwing``` 을 명시해준다. ```throwing = "exception"``` 의 ```exception``` 은 
메소드의 파라미터 중 예외 객체가 담길 파라미터의 명칭과 동일해야 한다.

테스트를 위해 테스트 클래스에 메소드를 하나 추가한다.

```java
@SpringBootTest
public class AopTest {

    @Autowired
    UserService userService;

    //...
    
    @Test
    @DisplayName("사용자가 없는 경우 NullPointerException 발생")
    void throwNullPointerException() {

        assertThrows(NullPointerException.class, () -> userService.findByUserId(4));
    }
}
```

테스트 결과는 다음과 같다.

```
2022-01-28 10:11:27.111  INFO 2044 --- [    Test worker] hwkang.study.springaop.AopTest           : Starting AopTest using Java 17.0.2 on DESKTOP-V1ME5NQ with PID 2044 (started by KHW-IPC in C:\Users\KHW-IPC\IdeaProjects\spring-aop)
2022-01-28 10:11:27.112  INFO 2044 --- [    Test worker] hwkang.study.springaop.AopTest           : No active profile set, falling back to default profiles: default
2022-01-28 10:11:28.870  INFO 2044 --- [    Test worker] hwkang.study.springaop.AopTest           : Started AopTest in 2.022 seconds (JVM running for 3.162)
2022-01-28 10:11:29.372  INFO 2044 --- [    Test worker] h.s.s.config.aop.MethodInfoAopConfig     :  [ UserServiceImpl.findByUserId ] start
2022-01-28 10:11:29.389 ERROR 2044 --- [    Test worker] h.s.s.config.aop.MethodInfoAopConfig     :  [ findByUserId ] NullPointerException occurred.
```
> 참고로 ```assertThrows``` 는 ```org.junit.jupiter.api``` 패키지의 ```Assertions``` 클래스이다.
> static import 를 통해 클래스 명을 명시하지 않고 메소드명 만으로 호출이 가능하다.
>
> ex) ```import static org.junit.jupiter.api.Assertions.*;```
>
> 굳이 이렇게 하는 이유는, 테스트를 위해 사용하는 ```Assertions``` 클래스가 같은 이름으로 서로 다른 패키지에 두 개 있기 때문이다.


### 사용자 정의 어노테이션이 붙은 메소드의 경우 실행 시간 출력하기

이번엔 어노테이션을 하나 생성하고, 그 어노테이션이 붙은 메소드의 실행 시간을 출력하는 예제를 만들어보겠다.

먼저 어노테이션을 하나 만들어준다.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ExecutionTime {

}
```

그리고 해당 어노테이션의 기능을 정의할 Config 클래스를 만들어준다. 

```java
@Aspect
@Configuration
@Slf4j
public class ExecutionTimeAopConfig {

    @Around("@annotation(hwkang.study.springaop.annotation.ExecutionTime)")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        MethodSignature methodSignature = (MethodSignature) proceedingJoinPoint.getSignature();

        String className = methodSignature.getClass().getSimpleName();
        String methodName = methodSignature.getMethod().getName();

        StopWatch stopWatch = new StopWatch(className + "." + methodName);
        stopWatch.start();
        Object result = proceedingJoinPoint.proceed();
        stopWatch.stop();

        long executionTime = stopWatch.getTotalTimeMillis();

        log.info(" [ " + stopWatch.getId() + " ] execution time is [ " + executionTime + " ] ms.");

        return result;
    }
}
```

**"메소드 실행 전에 ```StopWatch``` 실행 -> 타겟 메소드 실행 -> ```StopWatch``` 종료 -> ```StopWatch``` 실행 시간 출력"**
의 기능을 하는 메소드이다.

여기서는 ```@Around``` 어노테이션을 사용했다. 
```@Around``` 어노테이션은 메소드 실행 전과 후를 모두 제어할 수 있으며 결과값의 제어도 가능하다.

그럼 만든 ```@ExecutionTime``` 어노테이션을 사용해보자.

```java
@Service
public class UserServiceImpl implements UserService {

    final List<UserDto> userList = Arrays.asList(
            new UserDto(1, "khw"),
            new UserDto(2, "chr"),
            new UserDto(3, "kjh")
    );

    @Override
    @ExecutionTime
    public UserDto findByUserId(long userId) {
        return userList.stream()
                .filter(userDto -> userDto.getUserId() == userId)
                .findFirst()
                .orElseThrow(() -> new NullPointerException());
    }
}
```

사용법은 간단하다. 메소드에 해당 어노테이션만 추가해주면 된다.

```
2022-01-29 23:24:47.135  INFO 14952 --- [    Test worker] hwkang.study.springaop.AopTest           : Starting AopTest using Java 11.0.8 on DESKTOP-5H78H7R with PID 14952 (started by KHW in C:\Users\KHW\IdeaProjects\spring-aop)
2022-01-29 23:24:47.136  INFO 14952 --- [    Test worker] hwkang.study.springaop.AopTest           : No active profile set, falling back to default profiles: default
2022-01-29 23:24:48.710  INFO 14952 --- [    Test worker] hwkang.study.springaop.AopTest           : Started AopTest in 1.819 seconds (JVM running for 3.06)
2022-01-29 23:24:49.114  INFO 14952 --- [    Test worker] h.s.s.config.aop.MethodInfoAopConfig     :  [ UserServiceImpl.findByUserId ] start
2022-01-29 23:24:49.132  INFO 14952 --- [    Test worker] h.s.s.config.aop.ExecutionTimeAopConfig  :  [ MethodSignatureImpl.findByUserId ] execution time is [ 12 ] ms.
```

원하는 결과대로 잘 로그가 출려됨을 알 수 있다.

## 마치며

횡단 관심사를 구분하여 각각 모듈로 만들어 관리하는 것은 물론 좋은 발상이고, OOP 의 단점을 보완하는 좋은 프로그래밍 원칙이다.
하지만 AOP 를 무분별하게 사용할 경우 핵심 비즈니스 로직의 관리도 힘들 뿐더러 디버깅도 어려워진다.

물론 정답은 없겠지만, 관심사를 잘 나누고 적절한 위치에 적절한 프로그래밍 기법을 사용해 더 나은 프로그램을 만들도록 노력해야겠다.

[예제 소스코드 (GitHub)](https://github.com/hwkang93/spring-aop)

## Reference

[새로비 - [Spring] AOP (Spring AOP) 총정리 : 개념, 프록시기반 AOP, @AOP](https://engkimbs.tistory.com/746)

[Stack Overflow - Spring AOP vs AspectJ](https://stackoverflow.com/questions/1606559/spring-aop-vs-aspectj/35928254#35928254)

[Moon - AOP : Aspect Oriented Programming 개념](https://gmoon92.github.io/spring/aop/2019/01/15/aspect-oriented-programming-concept.html)

[Bale 의 Devlog - PointCut 표현식과 Advisor 구현](https://woongsin94.tistory.com/325)

https://ojt90902.tistory.com/720