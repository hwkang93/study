# Function vs Method


## Function

- Function 은 둘 다 소프트웨어에서 특정 동작을 수행하는 일정 코드 부분 즉, 코드를 통해 구현된 어떤 기능을 의미한다.
- Method 의 상위 포괄 개념이다.

  

## Method

- 클래스 내부에 정의된 Function 을 의미한다.
- Java 는 클래스 기반 객체지향 언어이다. 모든 메소드는 클래스 내에 정의되어 있으므로, Java 언어에서 사용하는 모든 함수는
메소드라고 할 수 있다.
- Method 는 선언 방식에 따라 Instance Method 와 Class Method 로 나뉜다.

> Function 은 어느 것(클래스, 열거형 등)에도 종속되지 않고, 항상 무상태성을 보장하는 기능 단위,
> Method 는 클래스에 종속된 Function, 객체 내에 선언된 Function 으로 Function 의 하위 개념 이라고 나름대로 정의하고 사용해왔다.
> 
> 그러다보니 **static 으로 선언된 Method 는 Method 냐 Function 이냐** 에 대한 궁금증이 생겼고, 열심히 찾아본 후 다시 정의해봤다.
> (결론은 static 으로 선언된 Method 는 Method 이다!)


## Reference

[함수 (프로그래밍)](https://ko.wikipedia.org/wiki/%ED%95%A8%EC%88%98_(%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D))

[Why is a static method considered a method? - StackOverflow](https://stackoverflow.com/questions/30402169/why-is-a-static-method-considered-a-method)

[What's the difference between a method and a function? - StackOverflow](https://stackoverflow.com/questions/155609/whats-the-difference-between-a-method-and-a-function)