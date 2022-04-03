# 객체지향 프로그래밍 5대 원칙

## 객체지향 프로그래밍이란?

객체지향 프로그래밍(Object-Oriented Programming) 줄여서 OOP 라고 한다.

객체지향 프로그래밍이란 다양한 프로그램 설계 기법 중 하나로, 객체를 중심으로 객체 간의 관계를 통해 프로그램을 구현하는 기법을 말한다. 



## 특징

### 추상화 (Abstraction)

객체들이 가지고 있는 속성 중에 공통된 속성을 한데 모아 정의하는 방법

### 상속 (Inheritance)

새로운 클래스가 기존의 자료와 연산을 이용할 수 있게 하는 기능.
상속을 통해 하위 클래스는 상위 클래스에서 정의한 필드 및 메소드 등을 사용할 수 있다.
- JAVA 는 다중 상속을 지원하지 않는다.


### 다형성 (Polymorphism)

어떤 한 요소에 여러 개념을 넣어 놓는 것으로 일반적으로 오버로딩이나 오버라이딩을 의미한다.
다형 개념을 통해 프로그램 안의 객체 간의 관계를 조직적으로 나타낼 수 있다.

- 오버로딩(overrode) : 같은 이름의 메소드가 인자의 개수나 자료형에 따라서 다른 기능을 하는 것
- 오버라이딩(override) : 같은 이름의 메소드가 여러 클래스에서 다른 기능을 하는 것


### 캡슐화 (Encapsulation)

내부의 동작이 어떻게 돌아가는지 모르더라도 사용법만 알면 쓸 수 있도록 클래스 내부를 감추는 기법.
클래스를 제공하는 사람은 인터페이스, 접근제어자 등을 통해 필요한 정보만 노출시키고, 내부 동작이 어떻게 구현되어 있는 지는 제공하지 않을 수 있다.

> **정보 은닉(Information Hiding)** : 클래스 내부의 데이터를 감추는 것


## SOLID

SOLID 란, 로버트 마틴이 2000년대 초반에 명명한 객체 지향 프로그래밍 설계의 다섯 가지 기본 원칙을 마이클 페더스가 두문자어 기억술로 소개한 것이다.

### SRP (Single Responsibility Principle) : 단일 책임의 원칙

한 클래스는 하나의 책임만 가져야 한다.

### OCP (Open Close Principle) : 개방폐쇄의 원칙

소프트웨어의 요소는 확장에는 열려있으나, 변경에는 닫혀 있어야 한다.

### LSP (The Liskov Substitution Principle) : 리스코브 치환의 원칙

프로그램의 객체는 프로그램의 정확성을 깨트리지 않으면서, 하위 타입의 인스턴스로 바꿀 수 있어야 한다.

### ISP (Interface Segregation Principle) : 인터페이스 분리의 원칙

특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.

### DIP (Dependency Inversion Principle) : 의존성 역전의 원칙

구체화에 의존하지 말고 추상화에 의존해야 한다.


## Reference

[객체 지향 프로그래밍 -위키백과](https://ko.wikipedia.org/wiki/%EA%B0%9D%EC%B2%B4_%EC%A7%80%ED%96%A5_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)

[객체지향 개발 5대 원리: SOLID - 넥스트리](https://www.nextree.co.kr/p6960/)

[SOLID (객체 지향 설계)](https://ko.wikipedia.org/wiki/SOLID_(%EA%B0%9D%EC%B2%B4_%EC%A7%80%ED%96%A5_%EC%84%A4%EA%B3%84))

[알고 보면 재밌는 객체 지향 프로그래밍, OOP 훑어보기](https://evan-moon.github.io/2019/08/24/what-is-object-oriented-programming/)