

# 스프링 프레임워크에서 의존성을 주입하는 방법

스프링 프레임워크에서 의존성을 주입하는 방법은 **생성자 주입, 수정자 주입, 필드 주입, 메소드 주입**으로 나눌 수 있다.

### 1. 생성자 주입 (Constructor Based Injection)

객체의 생성자를 통해 의존성을 주입하는 방법

```java
@Service
public class postServiceImpl implements OrderService {
	private final UserService userService;
    private final PostRepository postRepository;
	
    @Autowired
    public PostServiceImpl(UserService userService, PostService postService) {
        this.userService = userService;
        this.postService = postService;
    }
    
	...
}
```



생성자 주입은 **생성자 호출 시점에 딱 1번만 호출되는 것이 보장**된다.

의존관계가 변하지 않거나 필수인 경우에 주로 사용



생성자가 1개만 있을 경우에 ``` @Autowired ``` 어노테이션을 생략해도 주입 가능

```java
@Service
public class postServiceImpl implements OrderService {
	private final UserService userService;
    private final PostRepository postRepository;
	
    public PostServiceImpl(UserService userService, PostService postService) {
        this.userService = userService;
        this.postService = postService;
    }
    
	...
}
```



여기서 ```Lombok``` 라이브러리를 사용하면 소스코드를 더 정리할 수 있다.

```java
@Service
@RequiredArgsConstructor
public class postServiceImpl implements OrderService {
	private final UserService userService;
    private final PostRepository postRepository;
    
    ...
}
```

여기서 ```@RequiredArgsConstructor``` 어노테이션은 ```final```키워드를 갖는 필드로 생성자를 만들어주는 어노테이션이다.

>  Spring 4.3 버전부터는 생성자 주입을 권장하고 있다.

### 2. 수정자 주입 (Setter Based Injection)

**```Setter```라 불리는 필드의 값을 변경하는 수정자 메서드를 통해서 의존관계를 주입하는 방법**

의존관계를 선택해야 하는 경우나, 의존관계가 변경될 가능성이 있는 경우에 주로 사용한다.

여기서 ```setter```는 자바빈 프로퍼티 규약의 수정자 메서드를 말하는데,

흔히들 알고 있는 ```getter```, ```setter``` 에서의 ```setter```이다.



```java
@Service
public class postServiceImpl implements OrderService {
	private UserService userService;
    private PostRepository postRepository;
    
	@Autowired
    public void setUserService(UserService userService) {
        this.userService = userService;
    }
    
    @Autowired
    public void setPostRepository(PostRepository postRepository) {
        this.postRepository = postRepository;
    }
    
    ...
}

```

> 참고로 ```@Autowired```의 기본 동작은 주입할 대상이 없으면 오류가 발생한다.
>
> 주입할 대상이 없어도 동작하게 하려면 ```@Autowired(required = false)```로 지정하면 된다.



### 3. 필드 주입 (Field Injection)

필드에 의존관계를 주입하는 방법

```java
@Service
public class postServiceImpl implements OrderService {
    
    @Autowired
	private final UserService userService;
    
    @Autowired
    private final PostRepository postRepository;
    
    ...
}
```



필드 주입 방법은 코드가 간결하고 사용하기 간편하여 많은 개발자들이 사용했던 방식이나, 필드 주입은 스프링 프레임워크에 의존하고 있고, 외부에서 변경이 불가능하다는 단점을 가지고 있다.
이는 곧 단위테스트 코드 작성이 불가능해진다는 말과 같다.



#### 4. 일반 메서드 주입

일반 메서드를 통해서 의존관계를 주입하는 방법

일반 메서드 주입은 한번에 여러 필드를 주할 수 있는 특징을 가지고 있으며, 일반적으로는 잘 사용하지 않는다고 한다.
(나도 실무에서는 사용해본 적 없음. 개인프로젝트에서는 사용해본 적 있음)



```java
@Service
public class postServiceImpl implements OrderService {

	private final UserService userService;
    private final PostRepository postRepository;
    
    public void init(UserService userService, PostRepository postRepository) {
        this.userService = userService;
        this.postRepository = postRepository;
    }
    
    ...
}
```



> 스프링에서 제공하는 의존관계 자동 주입은 주입하려는 객체가 스프링 컨테이너가 관리하는 스프링 빈으로 등록되어 있어야 동작한다. 
> 스프링 빈으로 등록되지 않은 클래스에서 ```@Autowired``` 코드를 적용해도 아무 기능도 동작하지 않는다. 
> 업무나 학습 도중 의존관계 주입이 제대로 되지 않는 상황에 이런 경우도 한번 의심해보면 좋을 것 같다.



### 생성자 주입을 선택해야 하는 이유



#### 1. 객체의 불변성

대부분의 의존관계 주입은 최초 한번 일어나면 어플리케이션 종료 시점까지 의존관계를 변경할 일이 없다. 
오히려 의존관계는 어플리케이션이 종료될 때까지 변하면 안되는 경우가 대부분이다. 
생성자 주입은 어플리케이션이 실행될 때 딱 한번만 호출되므로(스프링 빈 객체로 등록되는 시점) 의존의 불변성을 지킬 수 있으며,
메모리적인 측면에서도 이익을 볼 수 있따.

> 물론 어플리케이션 실행 중에 의존관계를 변경해야 하는 일이 아예 없진 않다. 유도리있게 사용하기!

수정자 주입을 사용하면 ```Setter``` 메소드를 ```Public``` 으로 구현해야 하는데, 누군가 변경할 수 있는 여지가 남게 된다. 
변경하면 안되는 메소드를 열어두는 것은 좋은 설계 방법이 아니다. 
그리고 OOP의 5가지 개발원칙 중 OCP (Open-Closed Principal, 개방 폐쇄의 원칙)를 위반하게 되므로 생성자 주입을 통해 객체의 불변성을 보장하는 것이 좋다.



#### 2. final 키워드 사용 가능

생성자 주입을 사용하면 ```final``` 키워드 사용이 가능해진다. 
```final``` 키워드를 사용하면 객체의 초기화가 반드시 이루어져야 하며 만약 초기화가 이루어지지 않은 경우 컴파일 오류를 발생시킨다.

개발자는 컴파일 오류를 보고 누락된 의존성을 확인할 수 있다.

> 개발자에게 가장 좋은 오류 : **컴파일 오류**




#### 3. 테스트 코드의 작성이 용이

필드 주입으로 작성된 코드는 스프링에 의존하고 있어, 순수한 **자바 코드로 단위테스트를 작성하는 것**이 불가능하다.


#### 4. 순환참조 에러 방지

순환참조가 발생하는 코드의 경우, 생성자 주입으로 소스코드를 작성하면 어플리케이션 구동 시점에 에러를 발견할 수 있다. 
수정자 주입이나, 필드 주입으로 작성된 코드는, 해당 코드가 실행되는 시점에 순환참조 에러가 발생한다.

