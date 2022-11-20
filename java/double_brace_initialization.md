# 이중 중괄호 (Double brace initialization)



### 장점

- 생성 및 초기화 방식에 비해 적은 코드라인을 사용한다.
- 코드가 비교적 더 간결하다.
- 생성과 초기화를 동시에 진행할 수 있다.

### 단점

- 모호하고 널리 알려지지 않은 초기화 방법이다.
- 이중중괄호로 객체를 생성할 때 의도하지 않은 추가 클래스가 생성된다.
- Java 7에 도입된 기능인 "다이아몬드 연산자(```new HashMap<>()```)" 를 사용할 수 없다.
- 확장하려는 클래스가 "final" 으로 표시된 경우 사용할 수 없다.
- 둘러싸는 인스턴스에 대한 숨겨진 참조를 보유하므로 메모리 누수가 발생할 수 있다.

> 위와 같이 장점보다 단점이 더 많은 이유로 이중 중괄호를 **안티 패턴**으로 분류한다.

### 대안

#### 1. Java 8 Stream API 사용


#### 2. Java 9 Collection Factory

## Reference

https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=tmondev&logNo=220821035275

https://www.baeldung.com/java-double-brace-initialization