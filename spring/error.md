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

aspect 는 bean 을 둘러싼 프록시에 적용된다.
bean 에 대한 참조를 얻을 때마다 실제로 구성에서 참조하는 클래스가 아니라, 관련 인터페이스를 구현하고 클래스에 위임하고 AOP와 같은 기능을 추가하는
합성 클래스이다.

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

[참조 Stack overflow](https://stackoverflow.com/questions/13564627/spring-aop-not-working-for-method-call-inside-another-method)
