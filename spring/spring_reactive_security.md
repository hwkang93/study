#Spring Reactive Security

서블릿 어플리케이션에서 작동하는 스프링 시큐리티와, 스프링 리액티브 어플리케이션에서 작동하는 스프링 시큐리티는 구조가 조금 다르다.

다음은 스프링 시큐리티 설정 클래스이다.

#### SecurityConfig.java

```java
@Bean
public SecurityWebFilterChain securityFilterChain(ServerHttpSecurity http) {
    return http
            .csrf().disable()                                                                   // (1)
            .cors().configurationSource(corsConfigurationSource()).and()                        // (2)
            .exceptionHandling(exceptionHandlingSpec -> exceptionHandlingSpec                   // (3)
                    .authenticationEntryPoint(authenticationEntryPoint)                         // (4)
                    .accessDeniedHandler(jwtAccessDeniedHandler)                                // (5)
            )
            .formLogin().disable()                                                              // (6)
            .logout().disable()                                                                 // (7)
            .httpBasic().disable()                                                              // (8)
            .authorizeExchange(exchange -> exchange
                    .pathMatchers(PERMIT_ALL_PATTERNS).permitAll()                              // (9)
                    .anyExchange().authenticated()                                              // (10)
            )
            .securityContextRepository(NoOpServerSecurityContextRepository.getInstance())       // (11)
            .addFilterAt(authenticationWebFilter(), SecurityWebFiltersOrder.AUTHENTICATION)     // (12)
            .build();
}
```

우선, 기본적으로 스프링 시큐리티 필터 순서는 ```.addFilterAt(authenticationWebFilter(), SecurityWebFiltersOrder.AUTHENTICATION)``` 라인의
SecurityWebFiltersOrder enum 에 정의돼 있다. 개발자가 따로 시큐리티 설정을 하지 않는다면 SecurityWebFiltersOrder 에 정의된 순서대로 필터링이 진행된다.
순서는 다음과 같다.

```
1. HTTP_HEADERS_WRITER
2. HTTPS_REDIRECT
3. CORS
4. CSRF
5. REACTOR_CONTEXT
6. HTTP_BASIC
7. FORM_LOGIN
8. AUTHENTICATION
9. ANONYMOUS_AUTHENTICATION
10. OAUTH2_AUTHORIZATION_CODE
11. LOGIN_PAGE_GENERATING
12. LOGOUT_PAGE_GENERATING
13. SECURITY_CONTEXT_SERVER_WEB_EXCHANGE
14. SERVER_REQUEST_CACHE
15. LOGOUT
16. EXCEPTION_TRANSLATION
17. AUTHORIZATION
```

> **참고**
> 
> enum 클래스는 기본적으로 먼저 작성된 필드부터 0 이라는 ordinal 값을 갖고 있다.    
> **즉 필드마다 순서를 가지고 있다.**

위의 순서를 바탕으로 시큐리티 설정 클래스를 이해할 필요가 있다.

### (1) (6) (7) (8)

disable() 함수는 해당 필터는 사용하지 않겠다는 의미이다. 즉, 
```
(1) csrf().disable()      
(6) formLogin().disable()   
(7) logout().disable()   
(8) httpBasic().disable()   
```

은 각각 SecurityWebFiltersOrder 에 정의된 필터 중

```
4. CSRF
6. HTTP_BASIC
7. FORM_LOGIN
11. LOGIN_PAGE_GENERATING
12. LOGOUT_PAGE_GENERATING
15. LOGOUT
```

을 사용하지 않겠다는 의미이다.

### (3) (4) (5)

(3) (4) (5) 라인은 인증(Authentication) 또는 인가(Authorization) 가 실패한 경우 예외처리 클래스를 설정하는 파일이다.

스프링 시큐리티에서 디폴트로 제공하는 예외처리 클래스에는 응답 바디를 담을 수 없기 때문에 커스터마이징이 필요하다고 판단했고 클래스를 만들었다.

클래스는 구성은 다음과 같다. 두 클래스 모두 커스터마이징하는 이유가 동일하기 때문에 생김새가 비슷하다. 대표로 AccessDeniedHandler 클래스만 작성하겠다.

```java
/**
 * 403 예외 처리 클래스
 */
@Component
public class JwtAccessDeniedHandler implements ServerAccessDeniedHandler {

    private final ObjectMapper objectMapper = new ObjectMapper();

    @Override
    public Mono<Void> handle(ServerWebExchange serverWebExchange, AccessDeniedException e) {
        ServerHttpResponse response = serverWebExchange.getResponse();

        response.getHeaders().setContentType(MediaType.APPLICATION_JSON);
        response.setStatusCode(HttpStatus.FORBIDDEN);

        return response.writeWith(Mono.fromSupplier(() -> {
            DataBufferFactory bufferFactory = response.bufferFactory();
            try {
                ErrorResponse errorResponseDto = new ErrorResponse(e.getMessage());
                byte[] errorResponse = objectMapper.writeValueAsBytes(errorResponseDto);

                return bufferFactory.wrap(errorResponse);
            } catch (Exception exception) {
                return bufferFactory.wrap(new byte[0]);
            }
        }));
    }

}
```

exchange 객체가 필터링 중 Access Denied (403) 오류가 발생하면 해당 핸들러로 도착한다.
그럼 예외 메시지를 조회하고 ErrorResponse 객체를 만들어 응답 바디에 담아서 리턴해주는 기능을 하고 있다.

### (9) (10)

(9) (10) 은, 스프링 필터에 진입하는 URL 에 대한 설정이다.

```
.authorizeExchange(exchange -> exchange
        .pathMatchers(PERMIT_ALL_PATTERNS).permitAll()                              // (9)
        .anyExchange().authenticated()                                              // (10)
)
```
```.pathMatchers(PERMIT_ALL_PATTERNS).permitAll()``` 은, PERMIT_ALL_PATTERNS (String[] 형태이다.) 패턴의 경우 인증 절차 없이 모두 승인하겠다는 의미이고
```.anyExchange().authenticated()``` 은, 그 외의 모든 요청은 권한 검사를 하겠다는 의미를 갖는다.
문자열 뿐만 아니라 HttpStatus 별로도 설정이 가능하며, 특정 Status + Pattern 형태로도 필터링이 가능하다.

### (11)

인증 객체는 스프링 컨텍스트에 에 저장되며, 스프링 컨텍스트에 저장된 인증 객체는 같은 요청을 받았을 때
이전에 거쳤던 복잡한 인증 절차 과정 없이 승인된다.

스프링에서 기본적으로 제공하는 세션 객체는
WebSessionServerSecurityContextRepository, NoOpServerSecurityContextRepository 두 개가 있으며
디폴트 값은 WebSessionServerSecurityContextRepository 로 ServerWebExchange 객체의 세션에 저장된다.
예제에서 NoOpServerSecurityContextRepository 로 설정한 이유는 인증 모듈이 무상태성을 띄기 위해서이다. 세션을 사용하게 되면, 서버에서 세션의 정보를 갖기 때문에 무상태성을 보장하지 않는다.

### (2)

cors 설정을 하는 부분이다. ```corsConfigurationSource()``` 구현 내용은 다음과 같다.

```java
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration configuration = new CorsConfiguration();
    configuration.setAllowedOrigins(Arrays.asList("*"));
    configuration.setAllowedMethods(Arrays.asList("GET","POST", "OPTIONS", "PUT", "DELETE"));
    configuration.setAllowedHeaders(Arrays.asList("*"));

    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", configuration);

    return source;
}
```

소스코드만 보면 느껴지겠지만 모든 HTTP 메소드 및 요청 도메인, 요청 헤더를 허용하겠다는 의미이다.
개발 과정에서는 모든 설정을 열어두고 개발을 진행해야 했기 때문에 모든 설정을 허용한 상태이다.


**Chat GPT 에 따르면..**


Reactive Security(반응형 보안) 에서는 Servlet Authentication 과는 다르게 AuthenticationManager - AuthenticationProvider 로의 흐름이 없다고 한다.

보통, 서블릿 시큐리티에서 AuthenticationManager 는 인증(authentication)을 처리하고 인증을 수행하는 로직을 AuthenticationProvider 에게 위임한다.
그리고 AuthenticationProvider 는 실제 인증을 수행하고 완전히 인증된 인증 객체를 AuthenticationManager 에게 반환한다.

하지만 반응형 보안에서는 이러한 책임 흐름이 다르다. 반응형 보안에서는 ReactiveAuthenticationManager 라는 새로운 인터페이스가 도입되었다. 

반응형 보안에서는 AuthenticationManager 가 인증 요청을 AuthenticationProvider 에게 위임하는 비슷한 역할을 수행하지만
AuthenticationProvider 는 실제 인증 로직을 수행하지 않는다. 대신, 실제 인증 로직은 일반적으로 ReactiveUserDetailsService 또는 사용자 정의 ReactiveAuthenticationManager 에서 수행된다.

결국 ReactiveAuthenticationManager 는 서블릿 시큐리티에서의 AuthenticationManager - AuthenticationProvider 를 한번에 진행한다고 할 수 있다.

> 그래서 Spring Security Document 에 ReactiveAuthenticationManager Architecture 가 없나보구나.

**그럼 서블릿 보안에서의 AuthenticationManager 1:N AuthenticationProvider 의 경우를 반응형 보안에서는 어떻게 처리해야 할까?**

반응형 보안에서 둘 이상의 인증 방법을 사용해야 하는 경우, 여러 개의 ReactiveAuthenticationManager 인스턴스를 생성하고 유형에 따라 AuthenticationManager 에게 위임하는 방법을 사용한다.

```java
public class MultiAuthenticationManager implements ReactiveAuthenticationManager {

    private final Map<Class<? extends Authentication>, ReactiveAuthenticationManager> authenticationManagers;

    public MultiAuthenticationManager(List<ReactiveAuthenticationManager> authenticationManagers) {
        this.authenticationManagers = authenticationManagers.stream()
                .collect(Collectors.toMap(manager -> manager.getClass().getInterfaces()[0].getGenericInterfaces()[0], Function.identity()));
    }

    @Override
    public Mono<Authentication> authenticate(Authentication authentication) {
        Class<? extends Authentication> authenticationClass = authentication.getClass();
        ReactiveAuthenticationManager authenticationManager = authenticationManagers.get(authenticationClass);
        if (authenticationManager != null) {
            return authenticationManager.authenticate(authentication);
        } else {
            return Mono.error(new BadCredentialsException("Unsupported authentication type"));
        }
    }
}
```

```java
@Configuration
public class SecurityConfig {

    @Bean
    public ReactiveAuthenticationManager jwtReactiveAuthenticationManager(JwtTokenProvider jwtTokenProvider, ReactiveUserDetailsService userDetailsService) {
        return new JwtReactiveAuthenticationManager(jwtTokenProvider, userDetailsService);
    }

    @Bean
    public ReactiveAuthenticationManager ldapReactiveAuthenticationManager(LdapUserSearch ldapUserSearch, PasswordEncoder passwordEncoder) {
        return new LdapReactiveAuthenticationManager(ldapUserSearch, passwordEncoder);
    }

    @Bean
    public ReactiveAuthenticationManager multiAuthenticationManager(List<ReactiveAuthenticationManager> authenticationManagers) {
        return new MultiAuthenticationManager(authenticationManagers);
    }
}
```

```java
@Bean
public SecurityWebFilterChain securityFilterChain(ServerHttpSecurity http) {
    return http
            .exceptionHandling(exceptionHandlingSpec -> exceptionHandlingSpec
                    .authenticationEntryPoint(jwtAuthenticationEntryPoint)  //401 예외처리 클래스 등록
                    .accessDeniedHandler(jwtAccessDeniedHandler))           //403 예외처리 클래스 등록
            .csrf().disable()                                               //인증이 세션 방식이 아니므로
            .formLogin().disable()                                          //로그인 화면 X
            .authorizeExchange(exchange -> exchange
                    .pathMatchers(HttpMethod.OPTIONS).permitAll()       //HttpMethod.Options 권한은 모두 허용한다.
                    .pathMatchers("/auth/**").permitAll()    // '/auth/**' 로 시작하는 요청은 필터링하지 않는다.
                    .anyExchange().authenticated() //그 외의 모든 요청은 필터링을 진행한다.
            )
            .securityContextRepository(NoOpServerSecurityContextRepository.getInstance())   //스프링 시큐리티에서 제공하는 세션 사용 X
            .addFilterAt(authenticationWebFilter(), SecurityWebFiltersOrder.HTTP_BASIC)    //JWT 인증 필터 추가 (/auth/** 이외의 모든 요청에서 인증 진행)
            .build();
}
```

## Reference

https://docs.spring.io/spring-security/reference/reactive/index.html