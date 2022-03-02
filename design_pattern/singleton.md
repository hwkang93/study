# Singleton Pattern

싱글톤 패턴(Singleton Pattern)은 생성자가 최초로 호출되었을 때 객체가 한 번만 만들어지며 
이후에는 객체를 새로 생성하는게 아니라 최초에 만들어졌던 객체를 리턴하는 방법으로 객체를 사용하는 패턴이다.

싱글톤 객체는 static 으로 선언되어 Method area 에 올라와있기 때문에 프로그램 전역에서 해당 클래스의 인스턴스를 바로 얻을 수 있고, 객체를 딱 한번만 호출하기 때문에 불필요한 메모리 낭비를 최소화한다.

## 싱글톤 패턴 구현 방법

싱글톤 패턴을 구현하는 방법에는 Eager initialization(사전 초기화), Lazy initialization(사후 초기화) 등이 있다.

### Eager initialization (사전 초기화)

클래스 로딩 시에 인스턴스를 생성하는 방법이다. 사전 초기화 방법은 멀티스레드 환경에서의 이중 객체 생성 문제가 없지만,
인스턴스를 호출하지 않아도 무조건 클래스를 초기화하기에 메모리 효율이나 연산 효율은 낮다.

> **이중 객체 생성 문제**
> 
> 거의 비슷한 시간에 두 스레드가 동시에 싱글톤 인스턴스에 접근 후 생성된 인스턴스가 없다는 것을 확인하고 인스턴스를 생성하다가 싱글톤임에도 불구하고 객체가 중복 생성되는 문제

### Lazy initialization (사후 초기화)

인스턴스를 실제로 사용하는 시점에 인스턴스를 생성하는 방법이다. 인스턴스를 실제로 사용하지 않는다면 메모리와 연산량을 아낄 수 있다는 장점이 있지만,
세심한 방법을 쓰지 않으면 이중 객체 생성 문제가 발생할 가능성이 높다.



## 싱글톤 패턴 구현 예제

### Eager initialization

다음은 사전 초기화 방식으로 싱글톤 객체를 구현하는 가장 대표적인 방법이다.

```java
public class Singleton {
    
    //클래스가 로딩될 때 딱 한 번만 생성하기 위해 static 으로 선언
    //객체의 참조값을 지워버리는 행위(Classname.singleton = null)를 막기 위해 final 로 선언
    private static final Singleton instance = new Singleton();
    
    //Singleton 클래스의 인스턴스를 호출하기 위한 함수
    public Singleton getInstance() {
        return instance;
    }
    
    //생성자는 호출 불가능하도록 private 접근 제어자로 막음
    private Singleton(){}
}
```

```java
public class SingletonMain {
    public static void main(String[] args) {
        //Singleton 객체를 호출하는 방법
        Singleton singleton1 = Singleton.getInstance();
        
        //여러 개의 singleton 인스턴스를 만들더라도 해당 인스턴스들은 모두 같은 메모리 주소를 가리킨다.
        Singleton singleton2 = Singleton.getInstance();
        Singleton singleton3 = Singleton.getInstance();
    }
}
```

### Lazy initialization

사전 초기화 방식으로 객체를 초기화하게되면, 어플리케이션이 시작되는 시점에 **모든 객체**가 메모리에 올라가게 된다.
그렇게 되면 어플리케이션 기동 속도가 늦어지게 되고, 사용되지 않는 객체도 모두 메모리에 올라가기 때문에 메모리 사용을 비효율적으로 하게 되는데,
이러한 문제점을 보완하기 위해 나온 방법이다.

```java
public class Singleton {
    private static Singleton singleton;
    
    public static Singleton getInstance() {
        if(singleton == null) {
            return new Singleton();
        }
        
        return singleton;
    }


    private Singleton() {}
}
```

```getInstance()``` 메소드를 호출할 때 ```Singleton``` 객체가 있는 지 확인하고,
만약 ```Singleton``` 객체가 최초 호출이면 새로운 ```Singleton``` 인스턴스를 생성하여 리턴하고
호출한 적이 있으면 최초에 생성했던 ```Singleton``` 인스턴스를 리턴해주는 예제 소스코드이다.

위의 예제는 큰 문제를 가지고 있는데, 바로 **Thread Safe 하지 않다**는 것이다. 
멀티 쓰레드 환경에서 둘 이상의 사용자가 동시에 ```Singleton.getInstance()```메소드를 호출하는 경우 하나의 ```Singleton``` 인스턴스만 만들어진다는 보장이 없다.

자바에서는 ```synchronized``` 라는 키워드를 제공하여 Thread Safe 를 가능하게 한다.

> **Synchronized**
> 
> 여러 개의 쓰레드가 한 개의 자원을 사용하고자 할 때, 현재 데이터를 사용하고 있는 해당 스레드를 제외하고,
> 나머지 쓰레드들은 해당 자원에 접근할 수 없도록 막는 개념.
> 
> 변수와 함수에 해당 키워드를 사용할 수 있으며, 내부적으로 Block, Unblock 을 처리해야 해서 성능 저하가 발생할 수 있다.

```synchronized``` 키워드로 Thread Safe 하게 소스코드를 수정하면 아래와 같다.

```java
public class Singleton {
    private static Singleton singleton;
    
    public static Singleton getInstance() {
        if(singleton == null) {
            synchronized (Singleton.class) {
                if(singleton == null) {
                    return new Singleton();
                }
            }
        }
        
        return singleton;
    }
    
    private Singleton() {}
}
```

### Initialization on demand holder idiom

Singleton 객체를 사전초기화하는 방법 중 성능이 가장 좋다고 알려진 방법이다. 

```java
public class Singleton {
    
    private static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
    
    private Singleton() {}
    
    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }
    
}
```

이 방법은 JVM 의 클래스 로더의 특징을 살려 구현한 방법이다.

> 자바는 컴파일 타임이 아닌, 런타임 시점에 클래스를 처음으로 참조할 때 해당 클래스를 로드하고 링크하는 특징을 가지고 있다.

```private static``` 로 선언된 내부 클래스의 ```INSTANCE``` 인스턴스는 ```getInstance()``` 메소드가 최초 호출되는 시점에 생성되며 
JLS(Java Language Specification)에 의해 동기화가 보장된다.
또한 추가적으로 조회되는 ```getInstance()``` 메소드에 대해서는 동기화 오버헤드를 발생시키지 않는다.  



## 싱글톤 패턴의 문제점

1. 싱글톤 패턴을 구현하는 코드 자체가 많다.
2. 의존관계상 클라이언트가 구체 클래스에 의존한다.
3. 테스트하기 어렵다.
4. 내부 속성을 변경하거나 초기화하기 어렵다.
5. private 생성자로 자식 클래스를 만들기 어렵다.

> Java 의 스프링 프레임워크는 이러한 싱글톤 패턴의 문제점을 해결하여 객체를 싱글톤 패턴으로 구현된 Bean 으로 관리한다.







## Reference

[나무위키 - 디자인 패턴](https://namu.wiki/w/%EB%94%94%EC%9E%90%EC%9D%B8%20%ED%8C%A8%ED%84%B4)

[코딩팩토리 - 자바 - 동기화된 란? ~을?](https://coding-start.tistory.com/68)

[나무위키 - Initialization-on-demand_holder_idiom](https://en.wikipedia.org/wiki/Initialization-on-demand_holder_idiom)

http://literatejava.com/jvm/fastest-threadsafe-singleton-jvm/