# Spring Validation

> 현재 운영하는 API 서버의 유효성 검증이, 서비스 레이어에서 이루어지고 있었다.


클라이언트에서도 서버로 보낼 데이터들의 유효성을 검사하겠지만, 안전한 통신을 위해서는 서버에서도 데이터의 유효성을 검증할 필요가 있다.



## @Valid

- JAVA 표준 유효성 검증 어노테이션
- 제약 조건이 부여된 객체에 Bean Validator 를 이용해서 검증하도록 지시한다.
- Bean Validator 의 대표적인 구현체로 Hibernate Validator 가 있다.
  - 스프링부트에서 디폴트 구현체로 사용된다.
- DispatcherServlet > ArgumentResolver 에서 유효성 검증이 진행된다.
- 프론트 컨트롤러인 DispatcherServlet 에서 유효성을 검증하기 때문에 예외가 발생하면 400 에러를 리턴한다. (MethodArgumentNotValidException)
- 위와 같은 이유로 @Valid 는 컨트롤러에서만 동작한다.

## @Validated

유효성 검사는 MVC 모델 기준 Controller 계층에서만 진행되는 게 일반적이고, 바람직하다. 하지만 개발이 늘 그렇듯 예외 상황이 발생하기 마련이다.
이러한 예외 상황을 대비해 Spring 진영에서는 @Validated 라는 유효성 검증 어노테이션을 제공한다. 이 어노테이션은 AOP 기반으로 모든 계층에서 유효성 검증을 가능하게 도와준다.

다음은 @Validated 의 특징을 간략히 정리한 것이다.

- Spring Framework 에서 제공하는 유효성 검증 어노테이션
- AOP 를 기반으로 유효성 검증이 진행된다.
- 예외 발생 시 500 에러가 발생한다. (ConstraintViolationException)
- 컨트롤러 이외의 다른 계층에서 사용이 가능하다.
- 그룹을 지정하여 유효성 검증을 진행할 수 있음

## 주요 어노테이션

유효성 검증에 사용되는 주요 어노테이션이다. 
정리되지 않은 어노테이션에 관한 설명은 [이 곳](https://javaee.github.io/javaee-spec/javadocs/javax/validation/constraints/package-summary.html) 에서 확인할 수 있다.

- **AssertFalse** (Boolean 형) 해당 값은 반드시 false 여야 함
- **AssertTrue** (Boolean 형) 해당 값은 반드시 true 여야 함 
- **Email** (문자형) 해당 값은Email 형식이어야 함
- **Max** (숫자형) 해당 값은 어노테이션에 정의된 값 이하여야 함
- **Min** (숫자형)해당 값은 어노테이션에 정의된 값 이상이어햐 함
- **NotBlank** (문자형) 해당 값은 null 이면 안되고, 빈값이어도 안되고 공백이어도 안됨 (" " 도 안됨)
- **NotEmpty** (문자형)해당 값은 null 이면 안되고, 빈값이어도 안됨(" " 허용)
- **NotNull** (상관없음) 해당 값은 null 이면 안됨

각 어노테이션은 허용된 타입이 정해져있다. 허용되지 않은 타입에 어노테이션을 사용하는 경우 ~ 예외가 발생한다.

## 예제

아이디와 비밀번호를 변수로 갖는 로그인 요청 객체를 예제로 사용하려고 한다. 먼저 테스트케이스를 만들어보겠다.

```java
public class LoginValidationTest {

    final Validator validator = Validation.buildDefaultValidatorFactory().getValidator();

    @Test
    @DisplayName("아이디, 비밀번호가 정상적으로 입력된 경우 테스트에서 성공한다.")
    void success() {
        //1. given 정상적인 아이디, 비밀번호 부여하고
        final String id = "test01";
        final String password = "Test1234!";

        LoginRequestDto loginRequestDto = new LoginRequestDto(id, password);

        //2. when 유효성 검증하면
        Set<ConstraintViolation<LoginRequestDto>> validate = validator.validate(loginRequestDto);

        //3. then 유효성에 맞지 않는 결과 건수가 0이 나온다.
        Assertions.assertThat(validate.size()).isEqualTo(0);
    }
    
    //...생략
}
```

다음은 로그인 요청 객체에 유효성을 검사하는 어노테이션을 추가했다.

```java
@Getter
public class LoginRequestDto {

    @NotEmpty(message = "ID를 입력해주세요.")
    @Size(min = 4, max = 20, message = "사용자 ID는 4 ~ 20 글자 사이로 입력이 가능합니다.")
    private String userId;

    @NotEmpty(message = "비밀번호를 입력해주세요.")
    @Size(min = 8, max = 20, message = "사용자 ID는 8 ~ 20 글자 사이로 입력이 가능합니다.")
    private String password;

    @Builder
    public LoginRequestDto(String userId, String password) {
        this.userId = userId;
        this.password = password;
    }
}
```


### 디폴트 메시지 정의하기

MessageSource 를 Bean 으로 등록하여 메시지 처리를 공통 모듈화 시키는 방법


## 참고사항

### 형변환 등과 같은 오류가 발생하는 경우

```java
@Max(value = 10, message = "10 이하의 거리만 입력이 가능합니다.")
private int distance;
```

이러한 변수가 있다고 가정하자.

파라미터로 Integer 범위가 넘어가는, 예를 들어 1111111111 과 같은 값이 들어가면
Cast Error 가 발생함
이럴 때 해결 방안
1. message.properties 파일에 해당 디폴트 메시지 정의하기
2. Java 소스코드에서 예외 코드를 찾아 해당 코드에 맞게 메시지 분기 처리하기

내 경우 2번의 방법을 선택했다. 1번의 방법이 더 올바른 방법이라고 생각했지만
이미 많은 프로젝트에서 운영되고 있었고, 할당된 시간이 제한됐기 때문에 새로운 설정 파일을 운영 서버에 추가하는 것보다는 소스코드만을 수정하는 것이 더 리스크가 적을 것이라고 판단했다.
대안으로, 특정 코드마다 메시지를 정의하는 것이 아닌, 따로 정의하지 않은 모든 메시지를 디폴트 메시지('값이 올바르지 않습니다.' 등과 같은)로 설정하여,
예기치 못한 예외 상황에 대처하기로 했다.

시간의 여유가 좀 더 생기고 안정적인 배포가 가능해질 때, 수정할 예정이다.

### 주의사항

유효성 검사는 꼭 필요한 계층에서 꼭 필요한 데이터만으로 검증을 진행해야 한다.
검증 자체에 리소스가 소모되기 때문에 어플리케이션의 성능에 영향을 미칠 거라는 점을 항상 생각하면서 검증 절차를 추가해야 한다.

## Reference

[[SpringBoot] Spring Validation을 이용한 유효성 검증 - _koiil.log](https://velog.io/@_koiil/SpringBoot-Spring-Validation%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8%EC%84%B1-%EA%B2%80%EC%A6%9D)

[Spring Boot Bean Validation 제대로 알고 쓰자 - Continuous Growth](https://kapentaz.github.io/spring/Spring-Boo-Bean-Validation-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90/#)

[[Spring] Rest API Exception Handling - Dreamy 블로그](https://blog.naver.com/PostView.naver?blogId=writer0713&logNo=221605253778&parentCategoryNo=&categoryNo=83&viewDate=&isShowPopularPosts=true&from=search)

[[Spring] @Valid와 @Validated를 이용한 유효성 검증의 동작 원리 및 사용법 예시 - (1/2)망나니개발자](https://mangkyu.tistory.com/174)

[NHN Cloud - Validation 어디까지 해봤니?](https://meetup.toast.com/posts/223)