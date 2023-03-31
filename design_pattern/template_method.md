# Template Method Pattern

템플릿 메소드 패턴이란, 알고리즘의 구조를 메소드의 정의하고, 하위 클래스에서 알고리즘 구조의 변경 없이 알고리즘을 재정의하는 패턴이다.
알고리즘이 단계별로 나누어 지거나, 같은 역할을 하는 메소드이지만 여러 곳에서 다른 형태로 필요한 경우 고려해볼 수 있는 패턴이다.

## 장단점

### 장점

- 중복 코드를 줄일 수 있다.
- 자식 클래스의 역할을 줄여 핵심 로직 관리가 용이하다.
- 코드를 객체지향적으로 구성할 수 있다.

### 단점

- 추상 클래스와 구현 클래스의 결합도가 높다
  - 상속 때문에 하향적으로 결합도가 높아지고, 상위 클래스 메소드 사용도 반드시 필요해 상향적으로도 결합도가 높아진다.
- 추상메소드가 많아지면 클래스 관리가 복잡해진다.
- 클래스 간의 관계와 코드가 꼬여버릴 염려가 있다.


```java
abstract class Game {
    abstract void initialize();
    abstract void startPlay();
    abstract void endPlay();
 
    public final void play(){
        // 게임 초기화
        initialize();
 
        // 게임 시작
        startPlay();
 
        // 게임 종료
        endPlay();
    }
}
 
class Football extends Game {
    void initialize() {
        System.out.println("Football Game Initialized! Start playing.");
    }
 
    void startPlay() {
        System.out.println("Football Game Started. Enjoy the game!");
    }
 
    void endPlay() {
        System.out.println("Football Game Finished!");
    }
}
 
class Basketball extends Game {
    void initialize() {
        System.out.println("Basketball Game Initialized! Start playing.");
    }
 
    void startPlay() {
        System.out.println("Basketball Game Started. Enjoy the game!");
    }
 
    void endPlay() {
        System.out.println("Basketball Game Finished!");
    }
}
 
class Client {
    public static void main(String[] args) {
        Game game = new Football();
        game.play();
        
        System.out.println();
        
        game = new Basketball();
        game.play();
    }
}
```

## Reference

[디자인패턴-템플릿 메소드 패턴 - YABOONG](https://yaboong.github.io/design-pattern/2018/09/27/template-method-pattern/)

[[Design Pattern] 템플릿 메소드 패턴(Template Method Pattern)에 대하여](https://coding-factory.tistory.com/712)