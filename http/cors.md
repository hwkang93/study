# CORS (Cross-Origin Resource Sharing)

CORS 는 **웹 브라우저에서 실행되는 JavaScript 가 다른 도메인의 리소스를 요청할 때 브라우저에서 적용되는 보안 정책** 을 말한다. 
웹 브라우저는 동일 출처 정책(Same-Origin Policy)에 따라, 기본적으로는 다른 도메인의 리소스에 접근할 수 없다. 
그러나 CORS 를 허용하는 서버의 응답 헤더를 설정함으로써, 특정 도메인에서 오리진 간의 리소스 요청을 허용할 수 있다.
이를 통해 웹 애플리케이션에서 다른 도메인의 리소스를 안전하게 요청하고 사용할 수 있다.

CORS 는 서버에서 설정할 수 있으며, 브라우저는 서버의 요청에 따라 요청을 정의해야 한다.

다음은 Spring Security 에서 설정할 수 있는 CORS 예제이다.

```java
@Bean
public SecurityWebFilterChain securityFilterChain(ServerHttpSecurity http) {
        return http
            .csrf().disable()
            .cors()
            .configurationSource(corsConfigurationSource()).and()
            .build();
}

public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration configuration = new CorsConfiguration();
    configuration.setAllowedOrigins(Arrays.asList("*"));
    //configuration.setAllowedOrigins(Arrays.asList("*"));
    configuration.setAllowedMethods(Arrays.asList("GET","POST", "OPTIONS", "PUT", "DELETE"));
    configuration.setAllowedHeaders(Arrays.asList("*"));

    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", configuration);

    return source;
}
```




## 참고 예외

```
Access to fetch at 'http://localhost:8000/auth/generate' from origin 'http://출발지 도메인' has been blocked by CORS policy: 
The request client is not a secure context and the resource is in more-private address space `local`.
```

'http://출발지 도메인' 에서 게이트웨이인 'http://localhost:8000/auth/generate' 로 API 요청을 보낼 때 위의 에러가 발생하는 경우, 위와 같은 에러가 발생한다면
```localhost``` 대신 PC IP 로 요청하면 정상적인 결과를 받을 수도 있다.