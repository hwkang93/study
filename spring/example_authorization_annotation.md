# Authorization Annotation 예제 소스코드 (with AOP)

API 내에서 권한을 검증하는 예제 소스코드이다.

Authorization 이라는 어노테이션을 생성하고, 올바르지 않은 권한으로 요청하는 경우 403 에러를 리턴한다.   
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