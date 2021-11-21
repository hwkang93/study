# 함수형 인터페이스(Function Interface)


**함수형 인터페이스 (Functional Interface) 란, 추상 메소드가 하나만 있는 인터페이스를 의미한다.**

```java
@java.lang.FunctionalInterface
public interface AddCalculator {
    
    int add(int a, int b);
    
}
```

위의 예시처럼, 인터페이스를 하나 만들고 인터페이스에 ```@FunctionInterface``` 어노테이션을 붙여주면 함수형 인터페이스로 선언이 된다.
위 예시에서의 ```add(int a, int b)``` 처럼 추상 인터페이스에 선언된 메소드를 **추상 메소드** 라고 한다.
명시하지는 않았지만 ```add``` 메소드 앞에 ```abstract public``` 이 생략되어 있다.

<br/>

위에서 함수형 인터페이스란, 추상 메소드가 하나만 있는 인터페이스를 의미한다고 정의했다. 그럼 추상 메소드가 두 개 이상 있는 경우에는 어떻게 될까?

컴파일 오류가 발생한다.

```java
//Multiple non-overriding abstract methods found in interface functionalInterface.FunctionalInterface
@java.lang.FunctionalInterface
public interface AddCalculator {

    int add(int a, int b);
    
    int sub(int a, int b);
}
```

<br/>

그리고 추상 메소드가 아닌 다른 형태의 메소드(static, default)가 있더라도 추상 메소드가 하나만 있으면 함수형 인터페이스라고 할 수 있다.
```java
@java.lang.FunctionalInterface
public interface AddCalculator {

    int add(int a, int b);
    
    //default method
    default void print(int a, int b) {
        System.out.println("a = " + a);
        System.out.println("b = " + b);
    }
    
    //static method
    static void print(int a) {
        System.out.println("a = " + a);
    }
}
```

## Reference

[백기선 인프런 강의](https://www.inflearn.com/course/the-java-java8/dashboard)