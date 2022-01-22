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

### 메소드 실행 전 메소드 정보 로그로 출력하기

먼저 Aop 설정 파일을 만들어준다.
메소드 정보를 출력하는 설정 파일이기 때문에 ```MethodInfoAopConfig``` 라는 클래스 명으로 만들었다.

```java
@Aspect
@Configuration
@Slf4j
public class MethodInfoAopConfig {

    @Before("execution( * hwkang.study.springaop.user.service.UserServiceImpl.*(..))")
    void before(JoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();

        log.info(" [ " + methodName + " ] start");
    }
}
```


### 메소드 실행 후 에러가 발생했을 시 로그 쌓기 




### 사용자 정의 어노테이션이 붙은 메소드의 경우 실행 시간 출력하기



## 예시

* 어노테이션은 인터페이스에 붙이면 안됨.



## Reference

[새로비 - [Spring] AOP (Spring AOP) 총정리 : 개념, 프록시기반 AOP, @AOP](https://engkimbs.tistory.com/746)

[Stack Overflow - Spring AOP vs AspectJ](https://stackoverflow.com/questions/1606559/spring-aop-vs-aspectj/35928254#35928254)

[Moon - AOP : Aspect Oriented Programming 개념](https://gmoon92.github.io/spring/aop/2019/01/15/aspect-oriented-programming-concept.html)

[Bale 의 Devlog - PointCut 표현식과 Advisor 구현](https://woongsin94.tistory.com/325)