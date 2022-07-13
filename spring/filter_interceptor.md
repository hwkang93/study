# Filter vs Interceptor

![](https://user-images.githubusercontent.com/49870384/177259980-c2b338ef-d581-45d4-af69-3425a80c5eee.PNG)

## Filter

- 자바 표준
- 웹 컨테이너(Tomcat, JBOSS 등)에서 관리된다.
- Dispatcher Servlet 에 진입하기 전에 실행된다.
- 모든 요청에 대한 처리가 가능하다.
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
  - 필터 객체를 초기화하고 필터 객체들을 서비스에 추가하기 위해 정의하는 메서드이다.
  - 웹 컨테이너는 최초 1회 init 메서드를 호출한다.

- **doFilter**
  - 모든 HTTP 요청이 디스패처 서블릿으로 전달되기 전에 웹 컨테이너에 의해 실행되는 메서드이다.
  - filter 처리가 끝나면 doFilter 메서드 파라미터인 FilterChain 의 doFilter 메서드를 실행시켜 다음 필터 처리를 진행시킨다.

- **destroy**
  - 필터 객체를 삭제하기 위한 메서드이다.
  - 해당 메서드가 실행되면 메서드의 필터는 더이상 작동하지 않는다.

### Filter 사용처


### 기타

SpringBoot 에서는 웹 컨테이너도 스프링 컨테이너 내에서 관리된다. 그렇기 때문에 Filter 객체도 스프링 컨테이너에서 관리할 수 있다. 

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

- 스프링에서 지원하는 기술로 Dispatcher Servlet 이 Controller 를 호출하기 전과 후에 요청과 응답을 참조하거나 가공할 수 있는 기능을 제공한다.
- 새로운 request 와 response 객체를 생성하여 반환할 수 없다. 하지만 request 내의 값을 조작할 수는 있다.
- 예외 발생 시 Spring 에서 제공하는 예외처리, 예를 들어 ```@RestControllerADvice``` 등에서 예외 처리가 가능하다.


- **preHandle**

- **postHandle**

- **afterComplete**



## Reference

https://mangkyu.tistory.com/221