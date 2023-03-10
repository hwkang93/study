# 옵저버 패턴

옵저버 패턴(Observer Pattern)은 객체 사이의 일대다 의존 관계를 정의하는 디자인 패턴으로, 
어떤 객체의 상태 변화가 발생할 때 해당 객체에 의존하는 다른 여러 객체들에게 자동으로 알림을 보내는 메커니즘을 제공한다. 

이 패턴은 객체들 간의 느슨한 결합(Loose coupling)을 가능하게 하며, 이벤트 기반 시스템에서 많이 활용된다.

## 주요 요소

### 주체(Subject)

상태가 변화하는 주체 객체로, 옵저버들을 등록하고 상태 변화가 발생하면 등록된 옵저버들에게 알림을 보낸다.

### 옵저버(Observer)

주체 객체의 상태 변화를 감시하고, 상태가 변화했을 때 알림을 받는 객체이다.

주체 객체와 옵저버 객체 간에는 인터페이스를 통해 통신하며, 주체 객체는 옵저버들의 리스트를 관리한다.


## 옵저버 패턴의 구현 과정

- 주체 객체는 옵저버들을 등록하는 메서드와 해제하는 메서드를 제공한다.
- 주체 객체의 상태가 변화하면, 등록된 모든 옵저버들에게 알림을 보낸다.
- 각 옵저버는 주체 객체의 상태를 확인하고 필요한 동작을 수행한다.

## 옵저버 패턴의 장점

### 느슨한 결합

주체 객체와 옵저버들 사이에 인터페이스를 통해 통신하기 때문에, 객체들 간의 결합이 감소하여 유연성과 확장성이 향상된다.

###이벤트 기반 처리

이벤트가 발생했을 때 관련된 모든 옵저버들에게 알림을 보내므로, 이벤트 기반 시스템을 쉽게 구현할 수 있다.

### 중앙 집중화

여러 객체들의 동작을 중앙에서 관리하고 조정할 수 있으며, 코드의 중복을 줄일 수 있다.


## 구현 시 주의점

### 알림 순서

옵저버들에게 알림을 보내는 순서가 중요할 수 있으며, 이를 신중하게 고려해야 한다.

### 메모리 누수
주체 객체와 옵저버 사이의 강한 참조가 발생할 수 있으므로, 메모리 누수에 유의해야 한다.

옵저버 패턴은 GUI 프로그래밍, 이벤트 핸들링, MVC(Model-View-Controller) 패턴 등에서 많이 사용된다. 
이 패턴은 객체 지향 프로그래밍의 기본 원칙 중 하나인 "개방-폐쇄 원칙(Open-Closed Principle)"을 따르며, 
변경에는 닫혀 있고 확장에는 열려있는 구조를 제공하여 유연하고 확장 가능한 코드를 작성하는 데 도움을 준다.


## 예제 소스코드

```java
import java.util.ArrayList;
import java.util.List;

// 옵저버 인터페이스
interface Observer {
    void update(String message);
}

// 주제 클래스
class Publisher {
    private List<Observer> observers = new ArrayList<>();
    private String message;

    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }

    public void setMessage(String message) {
        this.message = message;
        notifyObservers();
    }

    private void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(message);
        }
    }
}

// 옵저버 클래스
class Subscriber implements Observer {
    private String name;

    public Subscriber(String name) {
        this.name = name;
    }

    @Override
    public void update(String message) {
        System.out.println(name + " received message: " + message);
    }
}

public class ObserverPatternExample {
    public static void main(String[] args) {
        // 주체 객체 생성
        Publisher publisher = new Publisher();

        // 옵저버 객체들 생성
        Subscriber subscriber1 = new Subscriber("Subscriber 1");
        Subscriber subscriber2 = new Subscriber("Subscriber 2");
        Subscriber subscriber3 = new Subscriber("Subscriber 3");

        // 옵저버들을 주체 객체에 등록
        publisher.addObserver(subscriber1);
        publisher.addObserver(subscriber2);
        publisher.addObserver(subscriber3);

        // 주체 객체의 상태가 변화하면, 등록된 옵저버들에게 알림을 보냄
        publisher.setMessage("Hello, observers!");

        // 한 옵저버를 제거한 후 다시 메시지 전달
        publisher.removeObserver(subscriber2);
        publisher.setMessage("Observer 2 removed. Hello again!");
    }
}
```