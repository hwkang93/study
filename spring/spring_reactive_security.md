#Spring Reactive Security




**Chat GPT 에 따르면..**

Reactive Security(반응형 보안) 에서는 Servlet Authentication 과는 다르게 AuthenticationManager - AuthenticationProvider 로의 흐름이 없다고 한다.

보통, 서블릿 시큐리티에서 AuthenticationManager 는 인증(authentication)을 처리하고 인증을 수행하는 로직을 AuthenticationProvider 에게 위임한다.
그리고 AuthenticationProvider 는 실제 인증을 수행하고 완전히 인증된 인증 객체를 AuthenticationManager 에게 반환한다.

하지만 반응형 보안에서는 이러한 흐름이 다르다. 반응형 보안에서는 ReactiveAuthenticationManager 라는 새로운 인터페이스가 도입되었다. 

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