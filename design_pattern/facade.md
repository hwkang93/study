# Facade

서브 모듈들에 대한 간략한 인터페이스를 제공하고자 할 때 사용하는 디자인 패턴. 간단한 인터페이스를 제공하여 복잡한 서브시스템을 감싸고,
간단한 방ㅂ식으로 사용할 수 있도록 하는 패턴을 말한다.

클라이언트는, 시스템 내부 구현 세부 정보를 알 필요 없이 해당 시스템을 인터페이스만으로 사용할 수 있다.

예를 들어, 어떤 시스템에서 데이터베이스 연결, 데이터베이스 쿼리 및 로깅과 같은 다양한 작업을 수행하는 서브시스템이 있다고 가정하자.
이러한 서브시트메에 대한 사용법을 모두 숙지하는 것은 매우 어렵고 복잡하며 시간 소모가 많을 수 있다. 따라서 Facade 패턴을 사용하면
이러한 서브시스템을 쉽게 사용할 수 있는 단순한 인터페이스를 제공할 수 있다. 이를 통해 클라이언트 코드는 Facade 인터페이스를 통해서만
서브시스템에 접근할 수 있으며, 구현 세부 정보는 감추어진다.   
이러한 방식으로 Facade 패턴은 시스템의 유지보수성을 향상시키고, 코드 재사용성을 높일 수 있습니다.

Wrapper 가 특정 인터페이스를 준수해야 하며, 폴리포픽 기능을 지원해야 할 경우에는 Facade 패턴보다는 Adapter 패턴을 사용해야 한다.
쉽고 단순한 인터페이스를 제공하고 싶을 경우에 Facade 패턴을 사용하는 것이 좋다.


## 예제 소스코드

다음은 자바로 작성된 예제 소스코드이다.

```java
// Subsystem 1
class Database {
    public void connect() {
        System.out.println("Connecting to database...");
    }
 
    public void disconnect() {
        System.out.println("Disconnecting from database...");
    }
}
 
// Subsystem 2
class Logger {
    public void log(String message) {
        System.out.println("Logging message: " + message);
    }
}
 
// Facade
class SystemFacade {
    private Database db;
    private Logger logger;
 
    public SystemFacade() {
        db = new Database();
        logger = new Logger();
    }
 
    public void start() {
        db.connect();
        logger.log("System started.");
    }
 
    public void stop() {
        db.disconnect();
        logger.log("System stopped.");
    }
}
 
// Client code
public class Client {
    public static void main(String[] args) {
        SystemFacade system = new SystemFacade();
        system.start();
        system.stop();
    }
}
```

## Facade 패턴과 Slf4j

Slf4j(Simple Logging Facade for JAVA) 는 이름에서부터 알 수 있듯이 Facade 패턴의 대표적인 예 중 하나이다.
Slf4j 는 간단한 인터페이스를 제공하여 다양한 로깅 시스템(log4j, java.util.logging 등)을 감싸고, 표준 로깅 API 를 제공하는 패턴이다.

SLF4J는 LoggerFactory 클래스와 Logger 인터페이스를 제공한다. LoggerFactory 클래스는 Logger 인터페이스의 구현체를 반환하는 팩토리 메서드를 제공하며, Logger 인터페이스는 간단한 로깅 API 를 정의한다. 
이를 통해 클라이언트 코드는 Logger 인터페이스를 통해 로그를 작성할 수 있으며, LoggerFactory 클래스는 Logger 인터페이스의 구현체를 반환하여 해당 로그를 적절한 로깅 시스템에 전달한다.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Example {
    private static final Logger LOGGER = LoggerFactory.getLogger(Example.class);

    public static void main(String[] args) {
        LOGGER.info("Hello, World!");
    }
}
```

[원본 이미지 바로가기](https://www.javatpoint.com/log4j-vs-slf4j)

## Reference

https://www.javatpoint.com/log4j-vs-slf4j

https://imasoftwareengineer.tistory.com/29

https://ko.wikipedia.org/wiki/%ED%8D%BC%EC%82%AC%EB%93%9C_%ED%8C%A8%ED%84%B4