# Optional

JAVA 8 에 추가된 null 값을 가지고 있을 수 있는 컨테이너 인스턴스 타입


- null 을 가질 수 있는 모든 객체에 대해 null 체크를 해야 한다. 
 -> 에러를 만들기 좋은 코드

- null 을 리턴하는 것 자체도 문제

- 에러를 발생시켜야 하는데 에러가 발생하면 자바에서는 stack trace 을 출력하고
이 자체에 리소스가 소모된다. 

> 로직을 처리하는 데 에러를 사용하는 것은 좋은 습관이 아니다.
> 쓸데없는 리소스를 소모하기 때문에

null 값이 전달될 수 있는 경우에 Optional 객체로 감싸서 리턴하면
클라이언트 입장에서 해당 객체가 null 일 수 있음을 인지할 수 있다.


## Optional 는 언제 사용하는 게 좋을까?

Optional 은 모든 위치에서 사용할 수 있지만, **리턴 타입에 사용하는 걸 권장한다.**

매개변수 타입으로 Optional<T> 를 사용하는 것은 권장되지 않는다.

그 이유는 Optional 객체 자체가 null 이 될 수 있고 문법적으로는 아무 문제가 없다. 
그렇기 때문에 코드상으로 객체에서 null 여부를 확인해야 하는데, 그럼 Optional 를 사용하는 의미가 없다.

**Map 객체에서 Key 값을 Optional 을 사용하지 않는다.**

Map 이 가지고 있는 가장 큰 특징이 **Map 객체에서 Key 값은 null 이 될 수 없다.**

**객체의 필드 값으로 사용하지 않는다.**

도메인 클래스 설계 단계에서 해결해야 할 문제이다.

**Optional 이 리턴 타입인 경우, 어떤 상황에서도 null 을 리턴하지 않는다.**

클라이언트에서 null 을 또 확인해야 한다. Optional 을 사용할 이유가 없다.
반환할 객체가 없는 경우 ```Optional.empty()``` 를 리턴한다.

Map, Stream, Collection, Array 등 다른 컨테이너 인스턴스는 비어있을 수 있는 객체이다.
Optional 로 다시 감싸면 박싱, 언박싱이 두 번 이루어져서 의미가 없다.
그렇기 때문에 Optional 로 감싸지 말 것.


primitive 타입의 Optional

```Optional.of(10)```

primitive 타입을 Optional 로 사용하는 것은 권장하지 않는다.

박싱, 언박싱을 두번씩 해야 하기 때문에 


## Optional 객체 사용법



## Reference

[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8/dashboard)