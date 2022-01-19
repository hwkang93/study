## no suitable constructor found for type

fetch API 를 사용하여 body 에 파라미터를 담아 POST 요청을 할 때 응답 클래스로 매핑되는 시점에 생성자가 없어서 발생하는 오류

디폴트 생성자 (롬복 라이브러리를 사용한다면 @NoArgsConstructor, 아니면 기본 생성자) 를 생성하면 됨.

## AOP 클래스 내의 다른 메소드를 호출할 때

예시 소스코드이다.

```java
@Component
public class A {
    public void a() {
        b();
    }
    
    public void b() {
        //...
    }
}
```

이런 소스코드가 있다고 가정할 때, a() 내에서 실행되는 b() 는 Aspect 포인트컷에 걸리지 않는다.

**Aspect 는 bean 을 둘러싼 프록시에만 적용되기 때문이다.**

> Bean 에 대한 참조를 얻을 때는, 실제로 작성된 클래스에서 참조를 하는 게 아니라 Spring 에서 생성한 Proxy 를 Bean 으로 등록한다.

위의 예에서 b() 메소드가 다른 인스턴스 객체에서 호출될 경우 aspect 가 적용이 된다.
혹은 b() 를 bean 객체로 만들어도 된다.

- 해결 예시

```java
@Component
public class A {
    
    @Autowired
    A self;
    
    public void a() {
        self.b();
    }
    
    public void b() {
        //...
    }
}
```

[참고 Stack overflow](https://stackoverflow.com/questions/13564627/spring-aop-not-working-for-method-call-inside-another-method)


## .jar 에 기본 Manifest 속성이 없습니다.

jar 파일로 export 할 때 Runnable JAR file 을 선택하고 export 하면 됨

## Eclipse 에서 스프링 부트 프로젝트 jar 로 말때는

프로젝트 마우스 오른쪽 > run as > maven build... 클릭해서 들어간 후에

package 입력 후 빌드하면 됨.


## org.apache.log4j 로그 객체에 예외 내용 담기

exception.printStackTrace() 는 로그 파일에 그 내용이 저장되지 않는다.

보통 에러로그(StackTrace)가 출력될 때, Console 에는 기본적으로 출력이 되는 것이며, Console Appender 가 출력하는 것이 아니다.

마찬가지로 File Appender 를 통해 File 에 따로 로그를 찍을때, printStackTrace 를 통해서 터져나온 에러가 파일에 저장되지 않는다.

때문에, 로그 파일에 예외 내용을 저장하고 싶을 때에는 아래와 같이 표현해야 한다.

```java

public class ExceptionExample {
    private final Logger log = Logger.getLogger(this.getClass());
    
    public void exceptionMethod() {
        try {
            throw new Exception();
        } catch (Exception e) {
            log.error("message", e);
        }
    }
}
```

[참고블로그](https://forgiveall.tistory.com/468)


