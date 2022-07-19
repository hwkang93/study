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

- 공통된 보안 및 인증/인가 관련 작업
- 모든 요청에 대한 로깅 또는 감사
- 이미지/데이터 압축 및 문자열 인코딩
- 스프링과 분리되어야 하는 기능

### DelegatingFilterProxy

Spring 1.2 버전 이상부터는, ```DelegatingFilterProxy``` 를 통해 필터를 스프링에서 관리할 수 있도록 지원한다.   
여기서 DelegatingFilterProxy 란, 서블릿 컨테이너에서 관리되는 프록시용 필터로 서블릿 컨테이너와 스프링 컨테이너를 잇는 프록시 역할을 하는 객체이다.   
사용자가 Filter 인터페이스 구현체를 만들어 스프링 빈으로 등록을 하면, 스프링 어플리케이션 구동 시 필터들이 빈 객체로 스프링 컨테이너에 등록이 된다.
모든 빈이 스프링 컨테이너에 정상적으로 등록이 되면 DelegatingFilterProxy 는 빈으로 등록된 Filter 의 구현체들을 찾아 자신에게 추가하고 서블릿 컨텍스트에 DelegatingFilterProxy 를 추가한다.   
이렇게 되면 요청에 대해 서블릿 컨텍스트는 DelegatingFilterProxy 에서 필터링을 진행하고, DelegatingFilterProxy 는 자신에게 등록된 필터들을 진행한다.

SpringBoot 에서는 서블릿 컨테이너마저도 스프링 컨테이너 내에서 관리된다. 
그렇기 때문에 SpringBoot 에서의 필터는 별도의 프록시 객체(DelegatingFilterProxy) 없이도 스프링 컨테이너에서 관리가 가능해졌다.


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
- 예외 발생 시 Spring 에서 제공하는 예외처리, 예를 들어 ```@RestControllerAdvice``` 등에서 예외 처리가 가능하다.


- **preHandle**

컨트롤러로 진입하기 전 실행되는 메서드이다. 데이터에 대한 전처리 작업 또는 데이터의 가공을 할 수 있다.
여기서 말하는 데이터의 가공은 새로운 HttpServletRequest, HttpServletResponse 객체를 만드는 것이 아닌 
기존 HttpServletRequest, HttpServletResponse 객체의 값을 변형하는 것을 말한다.
preHandle 의 3번째 파라미터인 handler 파라미터는 핸들러 매핑이 찾아준 컨트롤러 빈에 매핑되는 HandlerMethod 라는 새로운 타입의 객체로써, @RequestMapping 이 붙은 메소드의 정보를 추상화한 객체이다.

리턴 타입은 boolean 으로, true 값을 반환하면 다음 인터셉터 혹은 컨트롤러로의 진입을 가능하게 하며, false 인 경우 다음 작업을 진행하지 않는다.


- **postHandle**

Controller 에서 작업을 완료한 후 실행되며 데이터 후처리 또는 응답 객체의 조작을 할 수 있다.
과거에는 컨트롤러의 리턴 값이 화면인 경우가 많아 ModelAndView 를 파라미터로 받았지만, 최근에는 REST API 를 많이 사용하면서 해당 파라미터를 잘 사용하지 않는다.


- **afterComplete**

뷰의 생성 등 모든 작업이 완료된 후 실행되는 메서드이다.

### Interceptor 사용처

- 세부적인 보안 및 인증/인가 공통 작업
- API 호출에 대한 로깅 또는 감사
- 컨트롤러로 넘겨주는 데이터의 가공


## 마치며

어떤 하나의 개념을 이해하기 위해서는 그 개념을 위해 반드시 알아야 할 개념들을 익히는 데 시간이 더 소요되는 것 같다.



## Reference

https://mangkyu.tistory.com/221