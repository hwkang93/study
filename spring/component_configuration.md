# @Component vs @Configuration

설정 파일을 만들어 Spring Bean 객체로 등록할 때 사용하는 대표적인 어노테이션으로 ```@Component``` 와 ```@Configuration``` 이 있다.
두 어노테이션의 차이점이 궁금해 찾아보고 정리했다.

## @Configuration

- 개발자가 제어가 불가능한 외부 라이브러리를 Spring Bean 객체로 등록하고자 할 경우
- 프로젝트 초기 설정을 위한 경우
- ```@Configuration``` 내부에 ```@Component``` 어노테이션이 명시되어 있다.

## @Component

- 개발자가 직접 작성한 클래스를 Spring Bean 객체로 등록하고자 할 경우 사용


<br/>

```@Component``` 와 ```@Configuration``` 의 동작은 거의 동일하다. 두 어노테이션이 명시된 클래스를 Spring Bean 객체로 등록시키며
내부의 @Bean 어노테이션이 명시된 클래스들과 함께 스프링 컨텍스트에 등록시킨다.

- Configuration 사용 예시

```java
@Configuration
public static class Config {

    @Bean
    public SimpleBean simpleBean() {
        return new SimpleBean();
    }

    @Bean
    public SimpleBeanConsumer simpleBeanConsumer() {
        return new SimpleBeanConsumer(simpleBean());
    }
}
```

- Component 사용 예시

```java
@Component
public static class Config {

    @Bean
    public SimpleBean simpleBean() {
        return new SimpleBean();
    }

    @Bean
    public SimpleBeanConsumer simpleBeanConsumer() {
        return new SimpleBeanConsumer(simpleBean());
    }
}
```

어노테이션 종류만 다르고 같은 소스코드로 작성된 두 클래스의 기능은 동일하다. ```Config``` 클래스와
내부에 ```@Bean``` 으로 선언된 클래스, 즉 ```SimpleBean``` 클래스와 ```SimpleBeanConsumer``` 클래스를 
스프링 Bean 객체로 스프링 컨텍스트에 등록시킨다.

하지만 두 어노테이션은 ```simpleBeanConsumer()``` 메소드 동작 방식이 다른데, 
```@Configuration``` 의 경우 내부의 ```simpleBean()``` 에서 스프링 컨텍스트에 등록되어 있는 Bean 객체가 반환되는데,
```@Component``` 의 경우에는 새로 생성된 Bean 객체가 반환된다.

```@Component``` 의 ```simpleBeanConsumer()``` 메소드를 ```@Configuration``` 과 같이 동작하게 하려면 아래와 같이 작성하면 된다.

```java
@Component
public static class Config {
    @Autowired
    SimpleBean simpleBean;

    @Bean
    public SimpleBean simpleBean() {
        return new SimpleBean();
    }

    @Bean
    public SimpleBeanConsumer simpleBeanConsumer() {
        return new SimpleBeanConsumer(simpleBean);
    }
}
```

이렇게 작성하면 ```SimpleBeanConsumer``` 클래스를 스프링 Bean 객체로 등록할 때
기존 스프링 컨텍스트에 등록된 ```SimpleBean``` 객체를 파라미터 값으로 사용하게 된다.

이 차이는 CGLIB(Code Generation Library)이 두 어노테이션에서 다르게 동작하기 때문인데, 
기본적으로 ```@Bean``` 매소드들은 최초 한 번 호출되어서 생성된 객체가 스프링 컨텍스트에 저장이 되며, 
```@Configuration``` 애노테이션이 사용된 클래스 내부에서는 ```@Bean``` 메소드의 내부에서 호출한 메소드가 ```@Bean``` 메소드일 경우  
컨텍스트에 등록된 빈을 반환하도록 분기 처리가 되어있다고 한다.


## Reference

[하고 싶은게 있어!! - @Configuration vs @Component](https://m.blog.naver.com/sthwin/222131873998)

[망나니개발자 - [SpringBoot] @Bean, @Configuration, @Component 어노테이션](https://mangkyu.tistory.com/75) 