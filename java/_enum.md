> enum 은 메모리 관리가 어떻게 되는지 궁금해서 찾아봤고, 해답을 준 블로그

# Enum

Enum 상수의 집합을 의미한다.
열거형 이라고도 부르며 Java 1.5버전 부터 사용이 가능하다.

한정된 데이터 타입을 **Enumeration Type** 이라고 하며 열거 타입이라고 부른다.
열거 타입은 열거 상수(Enumeration constant)를 가질 수 있다.

```java
public enum Color {
    RED, GREEN, BLUE;
}
```

## Enum Memory

열거 상수는 내부적으로 ```public static final``` 키워드를 갖는다. 그렇기 때문에 Method 메모리 영역에 선언되어 있다.

Enum 객체 자체는 Heap 영역에 생성된다.

## 추가 속성

위에 선언한 ```Color``` 객체의 ```RED```, ```GREEN```, ```BLUE``` 는 차례대로 0, 1, 2 의 값이 설정된다.

Enum 에는 추가 속성을 만들 수도 있다.

```java
public enum Color {
    RED("#ff0000", 100, 0, 0),
    GREEN("#ff00ff", 0, 100, 0),
    BLUE("#0000ff", 0, 0, 100);
    
    private String hex;
    private short r;
    private short g;
    private short b;
    
    public Color(String hex, short r, short g, short b) {
        this.hex = hex;
        this.r = r;
        this.g = g;
        this.b = b;
    }
}
```

추가 타입을 Functional Interface 으로 설정할 수도 있다.

```java
public enum Calc {
    PLUS((a, b) -> a + b),
    MINUS((a, b) -> a - b);
    
    private BiFunction<Long, Long, Long> expression;
    
    public Calc(BiFunction<Long, Long, Long> expression) {
        this.expression = expression;
    }
    
    public long calculate(long a, long b) {
        
        return expression.apply(a,b);
    }
}
```


## Method

```enum``` 을 쓰면서 자주 사용했던 메소드들을 정리해보겠다.

```java
enum Auth {
    ADMIN, USER;
}
```

### name()

Enum 상수 객체의 이름을 조회하는 메소드이다.

```
Auth admin = Auth.ADMIN;
System.out.println(admin.name());
//출력 : ADMIN
```

### values()

```enum``` 의 스태틱 메소드이다.  ```enum``` 객체에 선언된 상수값을 배열 형태로 조회한다.

```
Auth[] auths = Auth.values();
for (Auth value : auths) {
    System.out.print(value.name() + " ");
}
//출력 : ADMIN USER
```



### 일반 메서드 정의 , Interface 구현

그리고 Enum 에는 일반 메서드를 정의할 수 있다. 또한, Interface 의 구현체가 될 수도 있다.

```java
public interface CodeMapper {
    int getCode();
    
    String getName();
}
```

```java
public enum UserAuth implements CodeMapper {
    USER(01, "일반사용자"),
    ADMIN(99, "관리자");
    
    public int code;
    public String name;
    
    public UserAuth(int code, String name) {
        this.code = code;
        this.name = name;
    }
    
    @Override
    public int getCode() {
        return this.code;
    }
    
    @Override
    public String getName() {
        return this.name;
    }
}
```

```Lombok``` 라이브러리를 사용해 소스코드를 조금 더 줄일 수 있다.

```java
@Getter
@AllArgsConstuctor
public enum UserAuth implements CodeMapper {
    USER(01, "일반사용자"),
    ADMIN(99, "관리자");
    
    public int code;
    public String name;
}
```



## Reference

[기록하는 개발자 - Java Enum 이란](https://honbabzone.com/java/java-enum/)