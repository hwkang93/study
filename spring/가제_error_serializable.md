#

로컬에서는 별 문제없이 잘 실행됐던 프로그램이 개발서버에서는 에러가 발생했다. 에러 내용은 아래와 같다.

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

해결 방법부터 설명하자면 데이터 객체에 Serializable 클래스를 import 해주면 된다.

```java
class BaseMapVO implements Serializable {
    //중략
}
```

## 왜 개발서버에서만 발생해?

로컬 서버 환경과 개발 서버 환경이 달랐기 때문이다.   
로컬 서버는 기본적으로 톰캣 한 대에 프로그램이 올라와있지만, 개발 서버는 두 대 이상의 서버를 통해 


## Reference

https://sungminhong.github.io/java/serialize/

https://devlog-wjdrbs96.tistory.com/268