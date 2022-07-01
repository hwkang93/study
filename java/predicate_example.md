# Predicate (Functional Interface) 를 이용해 로그인 가능 여부 메서드 구현하기

사용자 아이디와 비밀번호를 매개변수로 받아 사용자 정보를 조회하고, 
정상적으로 로그인이 가능한 사용자인지 판단하는 메서드를 만들어보고자 한다.
로그인 가능 여부 자체만의 메서드를 만들어 사용하는 것보다는 로그인 이라는 기능에 로그인 가능 여부를 함께 판단하는 게 더 일반적이겠지만,
그렇게 되면 이 글의 주제에 비해 너무 많은 양의 예제 소스코드가 필요하여 간략히 요점만 작성하기 위해 메서드의 범위를 축소시켰다.

여기서 고려해야 할 로그인 성공 or 실패 조건들은 다음과 같다.

1. 로그인에 **성공**하는 경우
   - 아이디, 비밀번호에 맞는 사용자 정보가 있고, 사용자 상태코드가 '정상' 인 경우

2. 로그인에 **실패**하는 경우
   - 로그인에 성공하는 하나의 경우를 제외한 모든 결과는 로그인 실패로 판단
   - 사용자 조회 결과가 없는 경우
   - 사용이 불가능한 계정인 경우
   - 관리자 승인 절차가 진행중인 경우
   - 기타

> 기타의 경우 로그인 성공 여부에 대한 로직을 설계하는 과정에서 예상하지 못했던 실패 원인이 있을 수 있는데,
> 이에 대한 가능성을 열어놓기 위해 추가하였다.

위의 조건을 토대로 메서드를 다음과 같이 구현해보았다.

```LoginService.java```

```java
@RequiredArgsConstructor
@Service
public class DBLoginService implements LoginService {
    
    private final LoginMapper loginMapper;
    
    /**
     * 로그인 가능 여부 확인
     */
    @Override
    public ValidLoginResponseEntity validateLogin(ValidLoginRequestDto requestDto) throws ApiException {
        String userId = requestDto.getUserId();
        String password = requestDto.getPassword();

        LoginUserResponseEntity responseUser = loginMapper.findByUserIdAndPassword(userId, password);
        
        if(isLoginSuccess(responseUser)) {
            //SUCCESS
        }
    }

    /*
        아이디, 패스워드에 일치하는 사용자 정보가 있고,
        UserSttusCode == 1 (사용자 상태코드 : 정상)
        UseAt == 'Y'       (사용 여부 : 정상)
        인 경우 로그인 가능
     */
    private boolean isLoginSuccess(LoginUserResponseEntity responseUser) {
        if(responseUser == null) {
            return false;
        }

        return "1".equals(responseUser.getUserSttusCode()) && "Y".equals(responseUser.getUseAt());
    }
}
```

```LoginServiceImpl.java```

```java
@Service
@RequiredArgsConstructor
public class LoginServiceImpl implements LoginService {

    private final LoginMapper loginMapper;
    private final ExternalResource externalResource;

    /**
     * 로그인 가능 여부 확인
     */
    @Override
    public ValidLoginResponseEntity validateLogin(ValidLoginRequestDto requestDto) throws ApiException {
        try {
            String userId = requestDto.getUserId();
            String password = requestDto.getPassword();
            String encryptPassword = EncryptPassword.encrypt(password, userId);

            LoginUserResponseEntity responseUser = loginMapper.findByUserIdAndPassword(userId, encryptPassword);

            //로그인이 가능한 경우 '로그인 성공 객체'를 리턴한다.
            if(isLoginSuccess(responseUser)) {
                return ValidLoginResponseEntity.createValidInstance();
            }

            //로그인이 불가능한 경우 로그인 조회 결과 정보로 로그인 실패 원인을 찾아 '로그인 실패 정보'를 리턴한다.
            return ValidLoginResponseEntity.createInvalidInstance(responseUser);
        } catch (Exception e) {
            throw new ApiException(ErrorType.ETC);
        }
    }

    /*
        아이디, 패스워드에 일치하는 사용자 정보가 있고,
        UserSttusCode == 1 (사용자 상태코드 : 정상)
        UseAt == 'Y'       (사용 여부 : 정상)
        인 경우 로그인 가능
     */
    private boolean isLoginSuccess(LoginUserResponseEntity responseUser) {
        if(responseUser == null) {
            return false;
        }

        return "1".equals(responseUser.getUserSttusCode()) && "Y".equals(responseUser.getUseAt());
    }
}
```

```ValidLoginResponseEntity.java```

```java
/**
 * 로그인 가능 여부 응답 객체
 *
 */
@ApiModel(value = "로그인 가능 여부 응답 객체")
public class ValidLoginResponseEntity {

   @ApiModelProperty(value = "로그인 가능 여부")
   private boolean isValid;

   @ApiModelProperty(value = "응답 메시지")
   private String message;

   private ValidLoginResponseEntity(boolean isValid, String message) {
      this.isValid = isValid;
      this.message = message;
   }

   public static ValidLoginResponseEntity createValidInstance() {
      return new ValidLoginResponseEntity(true, "");
   }

   public static ValidLoginResponseEntity createInvalidInstance(LoginUserResponseEntity responseUser) {
      return new ValidLoginResponseEntity(false, InvalidReason.findMessageBy(responseUser));
   }

   //Swagger UI > Response Example 에 정상적인 변수명 + @ApiProperty 속성 값을 보이게 하려면 Getter 메소드가 필요함
   public boolean isIsValid() {
      return this.isValid;
   }

   public String getMessage() {
      return this.message;
   }

   /**
    *  로그인 실패 원인 객체
    *
    *  추가로 로그인에 실패해야 하는 조건이 생길 경우 예외 조건을 포함한 필드(조건식, 응답메시지)를 작성한다.
    *  (필드 선언 순서대로 예외조건을 검사함)
    */
   private enum InvalidReason {
      //사용자 조회 결과가 없는 경우
      WRONG_PARAMETER(
              responseUser -> responseUser == null,
              "아이디 또는 비밀번호가 잘못 입력되었습니다."
      ),
      //사용이 불가능한 계정인 경우 (UseAt = N || userSttusCode = 2)
      NOT_USED(
              responseUser -> "N".equals(responseUser.getUseAt()) || "2".equals(responseUser.getUserSttusCode()),
              "사용 불가능한 계정입니다."
      ),
      //아직 관리자 승인을 받지 못한 계정인 경우 (UserSttusCode = 0)
      NEED_CONFIRM(
              responseUser -> "0".equals(responseUser.getUserSttusCode()),
              "관리자의 승인이 필요합니다."
      ),
      ETC(null, "관리자에게 문의해주세요.");

      //실패 조건
      private Predicate<LoginUserResponseEntity> check;

      //실패 메시지
      private String message;

      InvalidReason(Predicate<LoginUserResponseEntity> check, String message) {
         this.check = check;
         this.message = message;
      }

      private static String findMessageBy(LoginUserResponseEntity responseUser) {

         return Stream.of(InvalidReason.values())
                 .filter(reason -> reason.check != null)                 //InvalidReason.ETC 제외
                 .filter(reason -> reason.check.test(responseUser))      //실패 원인 찾기
                 .map(reason -> reason.message)                          //원인에 맞는 메시지 조회
                 .findFirst()                                            //메시지 출력
                 .orElse(ETC.message);                                   //원인을 찾지 못한 경우 기타 메시지
      }
   }
}
```