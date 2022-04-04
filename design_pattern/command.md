# Command

객체의 행위를 클래스로 만들어 캡슐화하는 패턴. 

객체의 행위 자체를 캡슐화(Command)하여 호출자 클래스(Invoker) 와 행위 구현 클래스(Receiver) 사이의 의존성을 제거한다.

행위 내역을 큐에 저장하거나 로그로 기록할 수도 있으며 작업 취소 기능도 구현할 수 있다.

## Command 패턴 구성 요소

- **Invoker**
  - 기능의 실행을 요청하는 호출자 클래스
  
- **Receiver**
  - ConcreteCommand 에서 정의한 기능을 실행하기 위해 사용되는 클래스
  
- **Client**
  - 어느 시점에 어떤 명령을 수행할 지 결정
  - 명령을 수행하기 위해 invoker 에 command 를 전달하고 수행
  
- **Command**
  - 실행될 기능의 오퍼레이션을 정의하는 인터페이스
  
- **ConcreteCommand**
  - 기능을 정의하는 클래스
  - Command 인터페이스의 구현체

## 장단점

### 장점

- 작업 요청과 수행을 분리할 수 있다.(SRP)
- 기존 코드를 수정하지 않고도 새로운 작업을 추가할 수 있다.(OCP)
- Undo / Redo 기능을 적용할 수 있다.
- 작업의 후처리를 정의할 수 있다.
- 작업 그룹을 지정할 수 있다.

### 단점

- 명령을 보내고 받는 새로운 계층을 정의하려고 한다면 코드가 복잡해 질 수 있다.

## 예제

구현된 커맨드 패턴을 사용하기는 해봤지만, 실제 프로젝트에서 커맨드 패턴을 구현한 적은 아직까지 없다.
그래서 마땅한 예제를 찾지 못했고, 다른 분들은 어떤 예제로 사용했는지 블로그들을 돌아다니면서 찾아봤는데
이 블로그의 예제가 커맨드 패턴을 잘 설명하고 있는 것 같아 참고해서 작성한다.

> 실제로 커맨드 패턴을 사용해 기능을 구현하는 일이 생기면 예제를 수정해야겠다.

블루투스 스피커와 전등과, 그 둘을 켜고 끄는 리모컨을 예제로 들어보겠다. 

### Receiver

실제로 수행될 기능이 정의된 클래스이다.
여기서는 블루투스 스피커와 전등이 Receiver 이다.

```BlueToothSpeaker.java```

```java
public class BlueToothSpeaker {
    private String location;

    public BlueToothSpeaker(String location) {
        this.location = location;
    }

    public void on() {
        System.out.println(location + "의 블루투스 스피커가 켜졌습니다.");
    }

    public void connectWithPhone() {
        System.out.println("휴대폰과 연결합니다.");
    }

    public void playingMusic() {
        System.out.println("음악을 켭니다.");
    }

    public void off() {
        System.out.println(location + "의 블루투스 스피커가 꺼졌습니다.");
    }
}
```

```Light.java```

```java
public class Light {
    private String location;

    public Light(String feature) {
        this.location = feature;
    }

    public void on() {
        System.out.println(location + "의 전등이 켜졌습니다.");
    }

    public void off() {
        System.out.println(location + "의 전등이 꺼졌습니다.");
    }
}
```

### Command

Command Interface 를 만들어 명령에 필요한 책임들을 설정한다.

```Command.java```

```java
public interface Command {

    /**
     * 명령을 실행한다.
     */
    void execute();

    /**
     * 이전 명령을 다시 실행한다.
     */
    void undo();
}
```

## ConcreteCommand

Command Interface 의 구현체이다.
예제에서는,
- 블루투스 스피커를 켜는 커멘드 구현체
- 블루투스 스피커를 끄는 커멘드 구현체
- 전등을 켜는 커멘드 구현체
- 전등을 끄는 커멘드 구현체
- 초기화를 위해 아무 기능도 들어있지 않은 커멘드 구현체

5가지로 구분하였다.



## 커멘드 패턴을 사용하는 패키지

- ```java.lang.Runnable```

- ```javax.swing.Action```

> **TODO**
> 
> 커멘드 패턴을 사용하면 Queue 사용하는 데 좋다고 하는데, 왜 좋은지 설명이 없음 ㅜㅜ...
> 찾아보고 정리하기.

## Reference

[커맨드 패턴(Command Pattern) - Philosophia](https://sun-22.tistory.com/16)




