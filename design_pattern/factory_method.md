# Factory Method

상위 클래스에 알려지지 않은 구체 클래스를 생성하는 패턴이며, 하위 클래스가 어떤 객체를 생성할지를 결정하도록 하는 패턴이기도 하다.
상위 클래스 코드에 구체 클래스 이름을 감추기 위한 방법으로도 사용된다.


## 장단점

### 장점

- 인스턴스의 타입을 결정하는 클래스가 따로 존재하여 객체들을 한 곳에서 관리할 수 있다는 장점이 있음
- 인터페이스가 정해져 있어 확장성이 용이함
- 구현체가 아닌 인터페이스에 의존하는 DIP 를 적용한 설계가 가능하다.

### 단점

- 새로운 타입의 인스턴스를 만들 때마다 구현체가 증가하여 복잡도가 증가할 가능성 존재 



## Effective Java 에서의 Static Factory Method

위에서 설명한 GoF 의 Factory Method 패턴은 Effective JAVA 에서 나오는 Static Factory Method 와 목적이 다르다.
둘 다 생성자를 대신 만들어주는 의미의 메소드로 Factory 를 사용하지만,
GoF 에서 말하는 Factory Method 패턴은 팩토리 메소드가 구현체 클래스의 결정을 하는 것을 목적으로 하고 있고,
Effective JAVA 에서의 Static Factory Method 는 new 키워드로 객체를 생성하는 것이 아닌, static 메소드를 활용해 객체를 생성하는 것을 목적으로 하고 있다.
(Effective JAVA 책을 다시 읽어보니 Factory Method 패턴과는 다른 개념이라고 설명 돼있었다.)

> **Effective JAVA 에서 말하는 Static Factory Method**
> 
> new 키워드로 인스턴스를 생성하는 것이 아닌 정적 메소드를 사용해 인스턴스를 생성하는 방식
> Static Factory Method 패턴의 장점으로는
> new 키워드로 만드는 생성자와는 달리 이름을 가질 수 있고,
> 호출할 때마다 새로운 객체를 생성하지 않아도 되고,
> 반환값 자료형의 하위 자료형 객체를 반환할 수 있고,
> ~~형인자 자료형(parameterized type) 객체를 만들 때 편하다.~~
> 

## Reference

[팩토리 메서드 패턴 - 위키백과](https://ko.wikipedia.org/wiki/%ED%8C%A9%ED%86%A0%EB%A6%AC_%EB%A9%94%EC%84%9C%EB%93%9C_%ED%8C%A8%ED%84%B4)