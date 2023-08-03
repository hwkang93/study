# @Value

application.properties (또는 yml) 파일에 정의된 프로퍼티들을 소스코드에서 사용할 수 있도록 지원하는 스프링 어노테이션이다.

## @Value 의 필요성

- 환경에 따라 유연하게 값을 설정할 수 있음
- 초기값을 설정할 수 있음
- 불필요한 컴파일을 줄일 수 있음

### 환경에 따라 유연하게 값을 설정할 수 있음

일반적으로 로컬 환경이나 알파/스테이지 환경 그리고 운영 환경에 따라 서로 다른 데이터베이스 서버를 가지고 있다. 
만약 환경에 따라 다른 값을 자바 코드만으로 설정해주려면 상당히 번거롭고 중복되는 코드가 상당히 많아진다. 
그래서 설정값은 별도의 파일(properties 또는 yaml)로 분리하고, 자바 코드에서는 환경 정보에 맞는 설정값을 불러오도록 하면 상당히 유용하다.

### 초기값을 설정할 수 있음

또한 불러오는 설정 값에 초기값을 지정해줄 수 있는데, 이를 통해 설정한 값이 불러지지 않은 경우에 대비할 수 있으며 테스트 작성을 용이하게 할 수 있다. 
즉, 초기값을 설정함으로써 테스트 작성에 유연하고 안전하게 개발을 할 수 있는 것이다.

### 불필요한 컴파일을 줄일 수 있음

만약 프로퍼티를 분리하지 않는다면 값이 클래스 안에 있을 것이다. 그럴때 만약 값이 수정되면 소스코드가 변하는 것이므로 다시 컴파일을 해주어야 할 것이다. 
하지만 설정 파일을 분리하고 @Value 로 참조하게 한다면 불필요한 컴파일없이 주입되는 값을 변경할 수 있다.

## 주의점

**@Value 어노테이션은 스프링 빈으로 등록된 인스턴스 내부에서만 정상적으로 어노테이션을 사용할 수 있다.** 
그렇기 때문에,
- static 으로 선언된 변수에 값을 넣거나, 
- 스프링 빈으로 등록되지 않은 클래스 내부에서 @Value 어노테이션을 사용할 경우

원하는 결과값이 아닌 ```null``` 을 반환한다.

그리고 ```@Value("${my.key}") private String myKey;``` 와 같이 사용하게 되는데 여기서 ```my.key``` 는 반드시 properties(또는 yml) 파일에 
선언되어 있어야 한다.

디폴트 값을 사용하고 싶은 경우 ```@Value(프로퍼티명:디폴트값)``` 의 형태로 입력하면 된다.


## 예제

다음은 @Value 어노테이션을 사용하는 예제이다. @Value 어노테이션을 사용하는 클래스는 반드시 스프링 빈으로 등록되어 있어야 한다.
예제에서는 @Component 어노테이션을 사용해 클래스를 스프링 빈으로 등록시켜 주고 있다.

### 직접 값 주입

```java

@Component
public class Example() {
    
    @Value("Hello!")
    private String message;
    
    @Value("1.234")
    private double doubleValue; 

    @Value("1234")
    private Integer intValue; 

    @Value("true")
    private boolean boolValue;

    @Value("2000")
    private long longValue;
}
```

### application.properties (yaml) 에서 정의한 값 주입

application.properties 에 다음 값을 정의했다.

```properties
hello.message=Hello!
```

```java

@Component
public class Example() {
    
    @Value("${hello.message}")
    private String message;
    
}
```

### List 주입

```properties
# application.properties
my.weekdays=Mon,Tue,Wed,Thu,Fri
```

```java
@Component
public class Example {
    
    @Value("${my.weekdays}")
    private List<String> strList; // injects [Mon, Tue, Wed, Thu, Fri]

    //Providing default value
    @Value("${my.weekends:Sat,Sun,Fri}")
    private List<String> strList2; // injec
}
```

### Map 주입

```properties
# application.properties
database.values={url:'http://127.0.0.1:3306/', db:'mySql', username:'root', password:'root'}
```

```java
@Component
public class Example {

    @Value("#{${database.values: {url: 'http://127.0.0.1:3308/', db: 'mySql', username: 'root', password: ''}}}")
    private Map<String, String> dbValues;

    @GetMapping("")
    public Map getDBProps(){
        return dbValues;
    }
    
}
```

### 생성자 파라미터 주입

```properties
# application.properties
company.name= Scopesuite Pty ltd
```

```java
@Service
public class CompanyService {
   private String compName;
   private String location;

   public CompanyService(
    @Value("${company.name}") String compName,
    @Value("${company.location:Washington}") String location
   ) {
       this.compName = compName;
       this.location = location;
   }
}
```

## Reference 

https://mangkyu.tistory.com/167

https://bcp0109.tistory.com/227

