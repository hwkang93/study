# Filter vs Interceptor

![](https://user-images.githubusercontent.com/49870384/177259980-c2b338ef-d581-45d4-af69-3425a80c5eee.PNG)

## Filter

- 자바 표준
- Dispatcher Servlet 에 진입하기 전에 실행된다.
- 모든 요청에 대한 처리가 가능하다
- 새로운 request, response 객체를 만들어 반환할 수 있다.
- 필터는 스프링 컨테이너 내에 있는 기능이 아니다. 그러므로 ```@ControllerAdvice``` 등과 같은 스프링에서 제공하는 예외 처리가 불가능하다.
- 

### Filter 사용 방법

Filter 를 사용하기 위해서는 ```javax.servlet``` 패키지 내의 Filter 인터페이스를 구현해야 한다.
다음은 Filter 인터페이스의 소스코드이다.

```java
package javax.servlet;

import java.io.IOException;

public interface Filter {
    default void init(FilterConfig filterConfig) throws ServletException {
    }

    void doFilter(ServletRequest var1, ServletResponse var2, FilterChain var3) throws IOException, ServletException;

    default void destroy() {
    }
}
```

- **init**

- **doFilter**

- **destroy**


## Interceptor

```java
package org.springframework.web.servlet;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.springframework.lang.Nullable;

public interface HandlerInterceptor {
    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        return true;
    }
    
    default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
    }

    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
    }
}
```

- 스프링에서 지원하는 기술로 Dispatcher Servlet 이 Controller 를 호출하기 전과 후에 요청과 응답을 참조하거나 가공할 수 있는 기능을 제공
- 새로운 request 와 response 객체를 생성하여 반환할 수 없다.


- **preHandle**

- **postHandle**

- **afterComplete**



## Reference

https://mangkyu.tistory.com/221