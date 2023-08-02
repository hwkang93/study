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

## CORS 요청의 종류

CORS 요청은 Simple/Preflight, Credential/Non-Credential 의 조합으로 4가지가 존재한다.

브라우저가 요청 내용을 분석하여 4가지 방식 중 해당하는 방식으로 서버에 요청을 날리므로, 프로그래머가 목적에 맞는 방식을 선택하고 그 조건에 맞게 구현해야 한다.

### Simple Request

아래의 3가지 조건을 모두 만족하면 Simple Request

- GET, HEAD, POST 중의 한 가지 방식을 사용해야 한다.
- POST 방식일 경우 Content-type 이 아래 셋 중의 하나여야 한다.
  - application/x-www-form-urlencoded
  - multipart/form-data
  - text/plain
- 커스텀 헤더를 전송하지 말아야 한다.

Simple Request는 서버에 1번 요청하고, 서버도 1번 회신하는 것으로 처리가 종료된다.

### Preflight Request

Simple Request 조건에 해당하지 않으면 브라우저는 Preflight Request 방식으로 요청한다.

따라서, Preflight Request 는

- GET, HEAD, POST 외의 다른 방식으로도 요청을 보낼 수 있고,
- application/xml 처럼 다른 Content-type 으로 요청을 보낼 수도 있으며,
- 커스텀 헤더도 사용할 수 있다.

Preflight Request 는 예비 요청과 본 요청으로 나뉘어 전송된다.
먼저 서버에 예비 요청(Preflight Request)를 보내고 서버는 예비 요청에 대해 응답하고,
그 다음에 본 요청(Actual Request)을 서버에 보내고, 서버도 본 요청에 응답한다.

하지만, 예비 요청과 본 요청에 대한 서버단의 응답을 프로그래머가 프로그램 내에서 구분하여 처리하는 것은 아니다.
프로그래머가 Access-Control- 계열의 Response Header 만 적절히 정해주면,
OPTIONS 요청으로 오는 예비 요청과 GET, POST, HEAD, PUT, DELETE 등으로 오는 본 요청의 처리는 서버가 알아서 처리한다.

### Request with Credential

HTTP Cookie와 HTTP Authentication 정보를 인식할 수 있게 해주는 요청

요청 시 xhr.withCredentials = true를 지정해서 Credential 요청을 보낼 수 있고,
서버는 Response Header에 반드시 Access-Control-Allow-Credentials: true를 포함해야 하고,
Access-Control-Allow-Origin 헤더의 값에는 *가 오면 안되고 http://foo.origin과 같은 구체적인 도메인이 와야 한다.

### Request without Credential

CORS 요청은 기본적으로 Non-Credential 요청이므로, xhr.withCredentials = true를 지정하지 않으면 Non-Credential 요청이다.

## 참고 예외

```
Access to fetch at 'http://localhost:8000/auth/generate' from origin 'http://출발지 도메인' has been blocked by CORS policy: 
The request client is not a secure context and the resource is in more-private address space `local`.
```

'http://출발지 도메인' 에서 게이트웨이인 'http://localhost:8000/auth/generate' 로 API 요청을 보낼 때 위의 에러가 발생하는 경우, 위와 같은 에러가 발생한다면
```localhost``` 대신 PC IP 로 요청하면 정상적인 결과를 받을 수도 있다.


## Reference

https://homoefficio.github.io/2015/07/21/Cross-Origin-Resource-Sharing/