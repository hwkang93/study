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


## Serializable(직렬화)란?

[우아한 형제들 기술블로그](https://techblog.woowahan.com/2550/) 에서 설명한 직렬화가 가장 와닿는 것 같아 발췌해왔다.

- 자바 직렬화란 자바 시스템 내부에서 사용되는 객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 바이트(byte) 형태로 데이터 변환하는 기술과 
바이트로 변환된 데이터를 다시 객체로 변환하는 기술(역직렬화)을 아우르는 단어이다.

- 시스템적으로 이야기하자면 JVM(Java Virtual Machine 이하 JVM)의 메모리에 상주(힙 또는 스택)되어 있는 객체 데이터를 바이트 형태로 변환하는 기술과
  직렬화된 바이트 형태의 데이터를 객체로 변환해서 JVM 으로 상주시키는 형태를 이야기한다.


## Reference

https://sungminhong.github.io/java/serialize/

https://devlog-wjdrbs96.tistory.com/268

https://techblog.woowahan.com/2550/