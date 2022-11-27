# Serializable

로컬에서는 별 문제없이 잘 실행됐던 프로그램이 개발서버에서 에러가 발생했다. 에러 내용은 아래와 같다.

```
01-Nov-2022 16:20:26.810 SEVERE [http-nio-7080-exec-10] org.apache.catalina.core.StandardWrapperValve.invoke Servlet.service() 
for servlet [action] in context with path [/admin] threw exception [Request processing failed; 
nested exception is java.lang.IllegalArgumentException: java.io.NotSerializableException: project.map.baseMap.vo.BaseMapVO] with root cause
        an exception which occurred:
        in object java.util.concurrent.CopyOnWriteArrayList@725f4c25
```

아래의 에러 메시지를 찾는다면 에러가 발생한 이유를 확실히 알 수 있다.

```
nested exception is java.lang.IllegalArgumentException: java.io.NotSerializableException
```
해당 클래스가 Serializable, 즉 직렬화가 되어있지 않다는 소리이다.   
해결 방법부터 설명하자면 데이터 객체에 Serializable 클래스를 import 해주면 된다.

```java
class BaseMapVO implements Serializable {
    //중략
}
```

## 왜 개발서버에서만 발생해?

로컬 서버 환경과 개발 서버 환경이 달랐기 때문이다.   
로컬 서버는 기본적으로 톰캣 한 대에 프로그램이 올라와있지만, 개발 서버는 두 대 이상의 서버가 있고 

직렬화에 대해 정리를 해보고자 한다.

## Serializable(직렬화)란?

[우아한 형제들 기술블로그](https://techblog.woowahan.com/2550/) 에서 설명한 직렬화가 가장 와닿는 것 같아 발췌해왔다.

- 자바 직렬화란 자바 시스템 내부에서 사용되는 객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 바이트(byte) 형태로 데이터 변환하는 기술과 
바이트로 변환된 데이터를 다시 객체로 변환하는 기술(역직렬화)을 아우르는 단어이다.

- 시스템적으로 이야기하자면 JVM(Java Virtual Machine 이하 JVM)의 메모리에 상주(힙 또는 스택)되어 있는 객체 데이터를 바이트 형태로 변환하는 기술과
  직렬화된 바이트 형태의 데이터를 객체로 변환해서 JVM 으로 상주시키는 형태를 이야기한다.

## 직렬화하는 방법

자바에서 객체를 직렬화하기 위해서는 해당 객체에 Serializable 인터페이스를 상속받으면 된다.

``` java
public class User implements Serializable {
  //...
}
```

## 직렬화의 장단점

### 장점

- 자바 기반 시스템에서 개발에 최적화되어 있다. (자바에서 제공하는 기술이므로 당연한 얘기지만)
- 복잡한 데이터 구조를 가지고 있는 객체라도 직렬화 기본 조건만 지키면 직렬화가 가능하다.

### 단점

- 객체의 필드 중 하나라도 매핑되지 않는 필드가 존재한다면 ```java.io.InvalidClassException``` 예외가 발생한다.
  => **자바 직렬화는 타입에 엄격하다.**
- 직렬화된 데이터는 타입에 대한 정보 등 클래스의 메타정보를 포함하고 있기 때문에 다른 포맷에 비해 용량이 크다.
- 자바 기반 시스템에서는 사용하기 좋지만 **자바에서만 사용할 수 있다.**

## 언제 직렬화를 사용하는 것이 좋을까?

JVM 메모리 위에서만 상주되어 있는 객체의 데이터가 영속화(Persistence)되어야 할 때 주로 사용된다.
시스템이 종료되더라도 사라지지 않는 장점을 가지며, 영속화된 데이터이기 때문에 네트워크 전송도 가능하다.
그리고 필요할 때 직렬화된 데이터를 가져와서 역직렬화하여 객체를 바로 사용할 수도 있다.

이러한 직렬화의 특성을 가진 자바 직렬화는 많은 곳에서 이용되는데, 주로 사용되는 곳은 다음과 같다.

- **Java RMI(Remote Method Invocation)**
  - 원격 시스템 간의 메시지 교환을 위해서 사용하는 자바에서 지원하는 기술
  - 원격 시스템과의 통신을 위해서 IP 와 포트를 이용해 소켓통신을 해야 하지만 RMI 는 그 부분을 추상화하여 
  원격에 있는 시스템의 메서드를 로컬 시스템의 메서드인 것처럼 호출할 수 있다.
  - 원격 시스템의 메서드를 호출 시에 전달하는 메시지(객체)를 **직렬화**시켜 전송한다.
  - 원격 시스템에서는 메시지를 **역직렬화**시켜 사용한다.

  > 최근에는 많이 사용되지 않지만 자바 직렬화의 개념 설명에 대표적인 예시로 사용된다고 한다. 

- **캐시(Cache)**
  - 캐싱하는 데이터를 직렬화하여 저장하고, 사용할 수 있다.

- **서블릿 세션 (Servlet Session)**
  - 서블릿 기반의 WAS 들은 대부분 세션의 자바 직렬화를 지원한다.
  - 서블릿 메모리 위에서 운용된다면 굳이 사용할 필요는 없지만, **세션 클러스터링, DB 저장 옵션 등을 선택하게 되면 세션 자체가 직렬화되어 저장된다.**

  > 서블릿 스펙에서 직접 기술한 내용이 아니므로 WAS 마다 동작이 달라질 수 있다.

## Reference

https://sungminhong.github.io/java/serialize/

https://devlog-wjdrbs96.tistory.com/268

https://techblog.woowahan.com/2550/