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

## Java 에서 제공하는 함수형 인터페이스

자바에서 기본적으로 제공하는 함수형 인터페이스들이다. ```Stream``` 를 사용하려면 알고 있어야 해서 기본적인 것 몇개만 끄적이겠다.

여기에 있는 것 외에 다른 함수형 인터페이스는 ```java.util.function``` 패캐지를 참고하자.

> **참고**
> 함수형 인터페이스의 인자들은 다른 제네릭 클래스들과 마찬가지고 기본 자료형(primitive type) 은 입력이 불가하다.
> Wrapper Class 나 Reference Type 만 가능하다.
> ex) int : 기본 타입 / Integer : Wrapper class

### Function<T,R>

- 파라미터 타입 T 를 입력받아 R 타입을 리턴하는 함수형 인터페이스
- 메소드로는 ```R apply(T t)``` 가 있다.

```
Function<String, String> introduce = name -> "My name is " + name;

System.out.println(introduce.apply("hwkang"));	
//결과 : My name is hwkang
```

### BiFunction<T,U,R>

- 첫번째 파라미터 타입 T, 두 번째 파라미터 타입 U 를 입력받아 R 타입을 리턴하는 함수형 인터페이스
- 메소드로는 ```R apply(T t, U u)``` 가 있다.

```
BiFunction<String, Integer, String> introduceNameAndAge =
                (name, age) -> "My name is " + name + "and age is " + age;

System.out.println(introduceNameAndAge.apply("hwkang",  29));
//결과 : My name is hwkangand age is 29
```

### Consumer< T >
- 파라미터 타입 T 를 입력받고, 리턴 타입이 없는 함수형 인터페이스
- 메소드로는 ```void accept(T t)``` 가 있다.

```
Consumer<String> introducePrint = name -> System.out.println("My name is " + name);

introducePrint.accept("hwkang");
//결과 : My name is hwkang
```

### Supplier< T >

- 파라미터는 없고, 리턴타입이 T 인 함수형 인터페이스
- 메소드로는 T get() 이 있다.

```
Supplier<String> name = () -> "hwkang";

System.out.println(name.get());
//결과 : hwkang
```

### Predicate< T >

- 파라미터 타입 T 를 입력받고, 리턴 타입이 ```boolean``` 인 함수형 인터페이스
- 메소드로는 ```boolean test(T t)``` 가 있다.

```
Predicate<String> isHwkang = str -> "hwkang".equals(str);

System.out.println(isHwkang.test("hwkang"));
//결과 : true
```

### BinaryOperator< T >

- BiFunction<T, U, R> 의 특수한 형태로 파라미터 타입과, 리턴 타입이 같은 함수형 인터페이스
- BinaryOperator< T > 는 BiFunction<T, T, T> 와 같다.
- 메소드로는 ```T apply(T t, T t)``` 가 있다.

```
BinaryOperator<Integer> sum = (num1, num2) -> num1 + num2;

System.out.println(sum.apply(5,4));
//결과 : 9
```

## 함수형 인터페이스의 사용 예제

다양한 상황에서 함수형 인터페이스를 사용해왔지만, 제 경험으로는 ```Stream```  객체를 사용할 때 함수형 인터페이스를 가장 많이 활용했던 것 같다.

예제 소스코드를 보시면서 '이런 경우에도 함수형 인터페이스를 사용할 수 있구나' 하고 봐주면 좋겠다.

```java
public class Main {
    public static void main(String[] args) {
        List<Integer> numberList = new ArrayList<>();

        //1~10까지의 값 배열에 저장
        for(int i=1; i<=10; i++) {
            numberList.add(i);
        }

        //5보다 큰 숫자
        Predicate<Integer> biggerThanFive = number -> number > 5;

        //메시지 형태로 변환
        Function<Integer, String> toMessage = number -> "5보다 큰 숫자들의 합은 [ " + number + " ] 입니다.";

        //두 숫자의 합
        BinaryOperator<Integer> sum = (num1, num2) -> num1 + num2;

        //5보다 큰 숫자들의 합을 구해서 메시지 형태로 출력하기
        String message = numberList.stream()
                .filter(biggerThanFive)     //5보다 큰 숫자들만 필터링
                .reduce(sum)                //숫자들의 합
                .map(toMessage)             //형식에 맞는 메시지로 변환
                .get();

        System.out.println(message);
    }
}
```

## Reference

[백기선 인프런 강의](https://www.inflearn.com/course/the-java-java8/dashboard)