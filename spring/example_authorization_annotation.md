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

- 정상적인 모든 API 요청의 파라미터에는 CommonRequestDTO 의 필드들이 존재한다.
- 비로그인 사용자의 경우 CommonRequestDTO 의 필드들이 "!GUEST" 라는 값으로 되어있다.
- 로그인한 모든 사용자는 "사용자 코드"를 가지고 있다. 사용자 코드는 LV1, LV2, LV3, ADMIN, PORTAL_ADMIN 으로 구분한다.
- 사용자 코드 별로 역할이 있으며 LV1, LV2, LV3 를 "일반 사용자", ADMIN 을 "관리자", PORTAL_ADMIN 을 "슈퍼관리자" 라고 정의한다.
- 모든 API 는 역할 별로 접근 여부를 판단한다.

## 구현

먼저 사용자 코드와 롤을 정의한다.   
사용자 코드는 enum 클래스로 구현한다. 아래 예제에서는 LV1, LV2, LV3, ADMIN, PORTAL_ADMIN 으로 구분한다.

```java
@Getter
@AllArgsConstructor
@NoArgsConstructor
public enum UserCode {
    LV1("1","LV1. 일반사용자"),
    LV2("12","2"),   //LV2. 실무담당자
    LV3("13","3"),   //LV3. 중간관리자

    INSTT_ADMIN("14","4"),  //LV4. 기관관리자

    MAPPICK_ADMIN("4","맵픽 관리자");    //맵픽관리자

    private String detailCode;
    private String detailCodeNm;

    public static UserCode of(String code) {
        return Arrays.asList(UserCode.values())
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

    MAPPICK_ADMIN(Arrays.asList(UserCode.MAPPICK_ADMIN)),

    INSTT_ADMIN(Arrays.asList(UserCode.LV3)),

    INSTT_USER(Arrays.asList(UserCode.LV1, UserCode.LV2, UserCode.LV3)),

    ALL(Arrays.asList(UserCode.LV1, UserCode.LV2, UserCode.LV3, UserCode.MAPPICK_ADMIN));

    private List<UserCode> userCodeList;
    
}
```

```Authorization.java```
```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Authorization {

    /**
     * 요청 객체(CommonRequestDTO)에 세션 정보가 있는지 확인한다.
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
    
    private String sessionUserCode;
    
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
                String userCode = commonRequestDTO.getSessionUserCode();
                if(!authorized(roles, userCode)) {
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
        String sessionUserCode = requestDTO.getSessionUserCode();

        log.debug("User guest check");
        log.debug("  => sessionInsttCode : " + sessionInsttCode);
        log.debug("  => sessionUserId : " + sessionUserId);
        log.debug("  => sessionUserCode : " + sessionUserCode);

        return guest.equals(sessionInsttCode) || guest.equals(sessionUserId) || guest.equals(sessionUserCode);
    }


    private boolean authorized(Role[] allowRoles, String userCode) {
        for(Role role : allowRoles) {
            if(role == Role.ALL) {
                return true;
            }

            List<UserCode> userCodeList = role.getUserCodeList();
            boolean hasRole = userCodeList.stream()
                    .map(UserCode::getDetailCode)
                    .map(detailCode -> detailCode.equals(userCode))
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