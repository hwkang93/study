# Authorization Annotation 예제 소스코드 (with AOP)

API 내에서 권한을 검증하는 예제 소스코드이다.

Authorization 이라는 어노테이션을 생성하고, 올바르지 않은 권한으로 요청하는 경우 403 에러를 리턴한다.   

> **Authorization**
>
> 인가. 권한 부여 라는 뜻으로 시스템 리소스에 대한 액세스 수준을 결정하는 데 사용되는 보안 매커니즘을 말한다.   
> **관리자는 "조회", "쓰기", "삭제" 권한**이 존재하고 **일반 사용자는 "조회" 권한이 존재**한다. 를 예로 들 수 있다.
> 
> 비슷하지만 헷갈리는 개념으로 Authentication 가 있다. 인증이라는 뜻으로 시스템을 사용하기 위해 필요한 신원을 확인하는 프로세스를 말한다.
> 대표적으로 "로그인" 을 예로 들 수 있다.
> 
> 참고로 권한 검증(Authorization) 실패에 대한 에러 코드는 403(Forbidden) 을,
> 사용자 인증(Authentication) 에 실패한 에러 코드는 401(Unauthorized) 을 주로 사용한다.

예제 소스코드는 다음 요구사항을 통해 구현되었다.

- 정상적인 모든 API 요청은 CommonRequestDTO 객체를 파라미터로 받는다.
- 비로그인 사용자의 경우 CommonRequestDTO 의 필드들이 "!GUEST" 라는 값으로 되어있다.


```Authorization.java```
```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Authorization {

    /**
     * 요청 객체(CommonRequestDTO)에 세션 객체가 있는지 확인합니다.<br/>
     */
    boolean sessionRequired() default false;

    Role[] allowRoles() default Role.ALL;
}
```

```CommonRequestDTO.java```

```java
@Getter @Setter
public class CommonRequestDTO {

    private String sessionUserId;
    
    private String sessionInsttCode;
    
    private String sessionUserSeCode;
    
}
```

```java
@Getter
@AllArgsConstructor
@NoArgsConstructor
public enum UserSeCode {
    INSTT_USER1("11","1"),   //LV1. 일반사용자
    INSTT_USER2("12","2"),  //LV2. 실무담당자
    INSTT_USER3("13","3"),  //LV3. 중간관리자

    INSTT_ADMIN("14","4"),  //LV4. 기관관리자

    MAPPICK_ADMIN("4","맵픽 관리자");    //맵픽관리자

    private String detailCode;
    private String detailCodeNm;

    public static UserSeCode of(String code) {
        return Arrays.asList(UserSeCode.values())
                .stream()
                .filter(useSeCode -> useSeCode.detailCode.equals(code))
                .findFirst()
                .orElse(null);
    }
}
```

```java
@AllArgsConstructor
@Getter
public enum Role {

    MAPPICK_ADMIN(Arrays.asList(UserSeCode.MAPPICK_ADMIN)),

    INSTT_ADMIN(Arrays.asList(UserSeCode.INSTT_USER3)),

    INSTT_USER(Arrays.asList(UserSeCode.INSTT_USER1, UserSeCode.INSTT_USER2, UserSeCode.INSTT_USER3)),

    ALL(Arrays.asList(UserSeCode.INSTT_USER1, UserSeCode.INSTT_USER2, UserSeCode.INSTT_USER3, UserSeCode.MAPPICK_ADMIN));

    private List<UserSeCode> userSeCodeList;
    
}
```


```AuthorizationException.java```

```java
public class AuthorizationException extends RuntimeException {
}
```

```AuthorizationAspect.java```

```java
@Component
@Aspect
@Slf4j
public class AuthorizationAspect {

    @Around("@annotation(authorization)")
    public Object Authorization(final ProceedingJoinPoint joinPoint, Authorization authorization) throws Throwable {
        Signature signature = joinPoint.getSignature();
        Object[] parameters = joinPoint.getArgs();

        Role[] roles = authorization.allowRoles();
        boolean sessionRequired = authorization.sessionRequired();

        for(Object parameter : parameters) {
            if(parameter instanceof CommonRequestDTO) {
                CommonRequestDTO commonRequestDTO = (CommonRequestDTO) parameter;

                //세션이 필수인데 게스트 요청인 경우 403
                if(sessionRequired && isGuest(commonRequestDTO)) {
                    throw new AuthenticationException();
                }

                //세션 기관 코드가 허용된 권한이 아니면 403
                //예를 들어 기관관리자만 사용할 수 있는 API 를 기관사용자가 요청한 경우 false
                String userSeCode = commonRequestDTO.getSessionUserSeCode();
                if(!authorized(roles, userSeCode)) {
                    throw new AuthenticationException();
                }
            }
        }

        return joinPoint.proceed();
    }

    private boolean isGuest(CommonRequestDTO requestDTO) {
        String guest = "!GUEST";

        String sessionInsttCode = requestDTO.getSessionInsttCode();
        String sessionUserId = requestDTO.getSessionUserId();
        String sessionUserSeCode = requestDTO.getSessionUserSeCode();

        log.debug("User guest check");
        log.debug("  => sessionInsttCode : " + sessionInsttCode);
        log.debug("  => sessionUserId : " + sessionUserId);
        log.debug("  => sessionUserSeCode : " + sessionUserSeCode);

        return guest.equals(sessionInsttCode) || guest.equals(sessionUserId) || guest.equals(sessionUserSeCode);
    }


    private boolean authorized(Role[] allowRoles, String userSeCode) {
        for(Role role : allowRoles) {
            if(role == Role.ALL) {
                return true;
            }

            List<UserSeCode> userSeCodeList = role.getUserSeCodeList();
            boolean hasRole = userSeCodeList.stream()
                    .map(UserSeCode::getDetailCode)
                    .map(detailCode -> detailCode.equals(userSeCode))
                    .findAny().orElse(false);

            if(hasRole) {
                return true;
            }
        }

        return false;
    }
}
```

```ApiExceptionHandler```

```java
@RestControllerAdvice
public class ApiExceptionHandler {

    @ExceptionHandler(AuthorizationException.class)
    public Response authorizationExceptionHandler(AuthorizationException e, HttpServletResponse response) {
        response.setStatus(HttpServletResponse.SC_FORBIDDEN);

        return ResponseHelper.error("API 요청 권한이 없습니다.");
    }

}

```