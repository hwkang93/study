# Adapter

**호환되지 않는 두 개의 인터페이스를 연결하는 역할**을 하는 클래스를 Adapter 라고 한다.
그리고 그 Adapter 클래스를 이용해 어떤 문제를 해결하는 방법을 Adapter Pattern 이라고 한다.

Adapter 패턴을 사용해야 하는 경우는 크게

- 외부 라이브러리 등의 구성 요소를 재사용하고 싶지만 현재 구현된 소스코드로는 호환이 불가능한 경우
- 인터페이스가 서로 달라 상호 호환이 안되는 경우

정도로 정의할 수 있다.

예시를 통해 Adapter Pattern 을 사용하는 방법을 알아보자.
예제는 이해하기 쉽도록 실생활에서 한 번 쯤은 접해봤을 법한 상황인 아이폰을 8 Pin - C Type 어댑터로 충전하는 과정으로 진행해보겠다.

## 예제 

Adapter Pattern 의 목적은 **호환되지 않는 두 개의 인터페이스를 연결**하는 것이다.
예제에서 사용할 호환되지 않는 두 개의 인터페이스는 C 타입 포트와, 8 Pin(아이폰 충전 포트) 포트이다.
두 인터페이스는 각각 충전하는 기능을 수행하는 "charge, chargeIPhone" 메소드를 가지고 있다.

인터페이스를 만들어보자.

```CTypePort.java```

```java
public interface CTypePort {

    void charge() throws InterruptedException;

}
```

```Pin8Port.java```

```java
public interface Pin8Port {

    void chargeIPhone() throws InterruptedException;
}

```

> throws InterruptedException 은 Thread.sleep 메소드를 사용하기 위해 처리한 예외이다.

8 Pin 충전 단자로 충전이 가능한 아이폰 객체를 만들어보겠다.

```IPhone.java```

```java
public class IPhone implements Pin8Port {

    private String name;

    public IPhone(String name) {
        this.name = name;
    }

    @Override
    public void chargeIPhone() throws InterruptedException {
        System.out.println("charging iphone...");
        Thread.sleep(1000);

        System.out.println( this.name + " is charged.");
    }
}
```

여기서 아이폰은 Pin8Port 의 구현체이며, ```chargeIPhone``` 메소드를 정의해야 한다.

간단하게 ```충전 시작 -> 1초 대기 -> 충전 완료```의 기능을 수행하도록 메소드를 정의했다.

이제 이 아이폰을 CType 충전 단자로 충전을 시켜야 하는데, 여기서 어댑터 패턴을 사용한다.

```CTypeAdapter.java```

```java
public class CTypeAdapter implements CTypePort {

    private final Pin8Port pin8Port;

    public CTypeAdapter(Pin8Port pin8Port) {
        this.pin8Port = pin8Port;
    }

    @Override
    public void charge() throws InterruptedException {
        pin8Port.chargeIPhone();
    }
}
```

```CTypeAdapter``` 클래스를 살펴보면 다음과 같다.

- ```CTypeAdapter``` 클래스는 ```CTypePort``` 클래스의 구현체이다.
- 생성자로 Pin8Port 클래스의 구현체를 파라미터로 받는다.
- ```CTypePort``` 의 ```charge``` 메소드를 ```Pin8Port``` 의 ```chargeIphone``` 메소드로 정의한다.

정리하면, ```CTypeAdapter``` 클래스는 ```Pin8Port``` 의 구현체를 파라미터로 받아,
```CTypePort``` 의 ```charge``` 메소드를 ```chargeIPhone``` 메소드로 실행시켜준다.

어댑터가 잘 구현됐는지 테스트를 해보자.

```AdapterPatternMain.java```

```java
public class AdapterPatternMain {

    public static void main(String[] args) throws InterruptedException {
        IPhone myIphone = new IPhone("my iphone");
        
        CTypeAdapter cTypeAdapter = new CTypeAdapter(myIphone);
        cTypeAdapter.charge();
    }
}
```

메인 메소드를 실행시키면 다음과 같은 결과가 출력된다.

```
charging iphone...
my iphoneX is charged.
```

## 정리

Adapter Pattern 은 호환되지 않는 두 개의 인터페이스를 Adapter 클래스를 통해 이어주고, 
메소드를 매핑시켜 마치 하나의 메소드를 실행시키는 것처럼 보이게 하는 패턴이다.
기존 클래스의 소스코드를 수정하지 않고 타겟 인터페이스에 맞춰 동작을 가능하게 하기 때문에 
기존 소스코드의 수정 최소화 또는 라이브러리를 입맛에 맞게 바꿔 사용하기 용이하다.

> 전체 예제 소스코드는 [깃허브](https://github.com/hwkang93/study-sample-source/tree/main/design-pattern/src/adapter_pattern) 참고하기


