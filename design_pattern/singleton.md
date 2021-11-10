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

다음은 싱글톤 객체를 구현하는 가장 대표적인 방법이다. (Eager initialization)

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


## 싱글톤 패턴의 문제점

1. 싱글톤 패턴을 구현하는 코드 자체가 많다.
2. 의존관계상 클라이언트가 구체 클래스에 의존한다.
3. 테스트하기 어렵다.
4. 내부 속성을 변경하거나 초기화하기 어렵다.
5. private 생성자로 자식 클래스를 만들기 어렵다.

> Java 의 스프링 프레임워크는 이러한 싱글톤 패턴의 문제점을 해결하여 객체를 싱글톤 패턴으로 구현된 Bean 으로 관리한다.







## Reference

[나무위키 - 디자인 패턴](https://namu.wiki/w/%EB%94%94%EC%9E%90%EC%9D%B8%20%ED%8C%A8%ED%84%B4)