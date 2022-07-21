# Proxy Pattern

프록시 패턴은 객체지향 언어의 특징 중 하나인 다형성을 이용해 객체지향 언어의 5대 원칙 중 하나인 단일 책임의 원칙(SRP)을 지키기 위해 고안된 패턴으로,
**어떤 인터페이스의 구현체가 너무 많은 책임을 가지고 있어 단일 책임의 원칙을 위반한다고 판단되는 경우, 프록시 객체를 통해 책임을 분산시키는 패턴이다.**

> [객체지향 언어의 특징 및 5대 원칙(SOLID) 간단 정리 바로가기](https://github.com/hwkang93/study/blob/main/java/oop.md)


## 프록시 패턴의 특징

- 프록시 클래스는 실제 구현체의 결과 그대로를 반환해야 한다. 값을 변형시키면 안된다.
- 프록시 클래스는 실제 구현체와 같은 이름의 메서드를 구현한다. 이때 인터페이스를 사용한다.
- 프록시 클래스는 실제 구현체에 대한 참조 변수를 갖는다(합성)
- 프록시 클래스는 실제 구현체의 같은 이름을 가진 메서드를 호출하고 그 값을 클라이언트에게 돌려준다.
- 프록시 클래스는 실제 구현체의 메서드 호출 전후에도 별도의 로직을 수행할 수도 있다.


## 프록시 패턴을 적용할 수 있는 경우

- 실제 구현체의 실행 시점을 제어해야 하는 경우
- 기존 소스코드의 수정 없이 전처리, 보안 계층 등의 작업을 추가해야 하는 경우
- 원래 객체가 다른 서버에 존재하고, 이 객체를 로컬 객체로 표현하고자 하는 경우 등


## 예제

프록시 패턴의 주 사용목적 중 하나인, 전처리 과정을 예로 들어 설명하려고 한다.

다음은 예제를 위한 요구사항 및 구현 방안이다.

- ID 를 통해 사용자 정보를 조회한다.
- 사용자 정보를 조회하는데 많은 비용이 소모된다.
- **소모되는 비용을 줄이기 위해 한번 조회한 정보는 캐싱한다.**

위의 경우(올바른 예시인지는 모르겠다) 사용자 정보를 조회하는 메서드에 한번 조회한 정보를 캐싱하는 기능이 추가될 것이다.
여기서 프록시 패턴을 사용해 정보를 캐싱하는 책임(기능)을 분리시켜보겠다.

```User.java```

```java
class User {
    private long id;
    private String name;
    private int age;

    public User(long id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
    
    public long getId() {
        return id;
    }
    public String getName() {
        return name;
    }
    public int getAge() {
        return age;
    }
}
```

```UserNotFoundException.java```

```java
class UserNotFoundException extends RuntimeException {
    private static final long serialVersionUID = 3979674380441745566L;

    public UserNotFoundException(String message) {
        super(message);
    }
}
```

```UserRepository.java```

```java
class UserRepository {

    private final Map<Long, User> testUserMap = new HashMap<Long, User>() {{
        put(1L, new User(1L, "khw", 30));
        put(2L, new User(2L, "chr", 30));
        put(3L, new User(3L, "kjh", 39));
        put(4L, new User(4L, "hds", 31));
        put(5L, new User(5L, "khj", 28));
    }};

    Optional<User> findById(long id) {
        return Optional.ofNullable(testUserMap.get(id));
    } 
}
```

```UserService.java```

```java
interface UserService {
	
    User findById(long id) throws UserNotFoundException;
	
}
```

```UserServiceImpl.java```

```java
class UserServiceImpl implements UserService{

    private final UserRepository userRepository;

    public UserServiceImpl() {
        this.userRepository = new UserRepository();
    }

    @Override
    public User findById(long id) throws UserNotFoundException {

        return userRepository.findById(id)
                .orElseThrow(() -> new UserNotFoundException("아이디가 [ " + id + " ] 인 사용자를 찾을 수 없습니다."));
    }
}
```

```ProxyUserService.java```

```java
class ProxyUserService implements UserService {
    
    private final UserService userService;

    public ProxyUserService() {
        this.userService = new UserServiceImpl();
    }
    
    private final Map<Long, User> cache = new HashMap<>();
    
    @Override
    public User findById(long id) throws UserNotFoundException {
        User user = cache.get(id);
        
        if(user == null) {
            user = userService.findById(id);
            cache.put(user.getId(), user);
        }
        
        return user;
    }
}
```

```ProxyExample.java```

```java
public class ExampleProxy {
    public static void main(String[] args) throws Exception {
        UserService userService = new ProxyUserService();

        User user1 = userService.findById(1L);
        User user2 = userService.findById(2L);

        System.out.println("user1.name : " + user1.getName());
        System.out.println("user2.name : " + user2.getName());
    }
}
```

'하나의 메서드는 하나의 책임만 가진다.' 라는 단일 책임의 원칙을 위반하지 않게 되고, 데이터 캐싱을 통해 불필요한 메모리 낭비도 막을 수 있다.



## Reference

https://today-retrospect.tistory.com/102#02

https://www.baeldung.com/java-proxy-pattern
