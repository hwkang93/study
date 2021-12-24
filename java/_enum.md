> enum 은 메모리 관리가 어떻게 되는지 궁금해서 찾아봤고, 해답을 준 블로그

# Enum

Enum 은 Java 1.5버전 부터 사용이 가능하다.

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

Enum 에는 추가 속성을 만들 수 있다.

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

## Reference

[기록하는 개발자 - Java Enum 이란](https://honbabzone.com/java/java-enum/)