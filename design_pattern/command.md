# Command

객체의 행위를 클래스로 만들어 캡슐화하는 패턴



- Invoker
  - 기능의 실행을 요청하는 호출자 클래스


- Receiver
  - ConcreteCommand 에서 정의한 기능을 실행하기 위해 사용되는 클래스

  
- Command
  - 실행될 기능의 오퍼레이션을 정의하는 인터페이스


- ConcreteCommand
  - 기능을 정의하는 클래스
  - Command 인터페이스의 구현체


- Client
  - 어느 시점에 어떤 명령을 수행할 지 결정
  - 명령을 수행하기 위해 invoker 에 command 를 전달하고 수행

## 장단점

### 장점

- 작업 요청과 수행을 분리할 수 있다.(SRP)
- 기존 코드를 수정하지 않고도 새로운 작업을 추가할 수 있다.(OCP)
- Undo / Redo 기능을 적용할 수 있다.
- 작업의 후처리를 정의할 수 있다.
- 작업 그룹을 지정할 수 있다.

### 단점

- 명령을 보내고 받는 새로운 계층을 정의하려고 한다면 코드가 복잡해 질 수 있다.


## 커멘드 패턴을 사용하는 패키지

- ```java.lang.Runnable```

- ```javax.swing.Action```

## Reference






