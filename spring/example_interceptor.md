# 인터셉터 예제 소스코드

servlet.xml

```xml
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:p="http://www.springframework.org/schema/p"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:mvc="http://www.springframework.org/schema/mvc"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
                http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
                http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd"
                >
    <!-- 중략 -->
    <mvc:interceptors>
	    	<mvc:interceptor>
	    		<mvc:mapping path="/**"/>
	    		<mvc:exclude-mapping path="/main"/>
	    		<bean class="인터셉터 디렉토리" />
	    	</mvc:interceptor>
    </mvc:interceptors>
</beans>
```

```java
public class AuthorInterceptor extends HandlerInterceptorAdapter{

	private static final Logger LOGGER = LoggerFactory.getLogger(AuthorInterceptor.class);

	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		LOGGER.info("Author Check.");
		LOGGER.info(" ==> reqeust user id : " + RedisSession.getUserVal("userId"));
		LOGGER.info(" ==> request URL : " + request.getRequestURI());

		String userId = RedisSession.getUserVal("userId");
		if(StringUtils.hasLength(userId)) {
			LOGGER.info("Author Check Success.");
			return true;
		}

		String responseURL = "/user/member/login?uri_redirect=";
		String requestURL = request.getRequestURI();
		String encodedRequestURL = new String(Base64.encodeBase64(requestURL.getBytes()));

		String redirectURL = responseURL + encodedRequestURL;

		LOGGER.info(" ==> redirect URL : " + redirectURL);
		LOGGER.info("Author Check Failed.");

		response.sendRedirect(redirectURL);

		return false;
	}

	@Override
	public void postHandle(HttpServletRequest request,
		HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

		super.postHandle(request, response, handler, modelAndView);
	}
}
```
