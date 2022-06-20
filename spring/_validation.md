# Spring Validation

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
정리되지 않은 어노테이션에 관한 설명은 [이 곳](https://javaee.github.io/javaee-spec/javadocs/javax/validation/constraints/package-summary.html)에서 확인할 수 있다.

- **AssertFalse** 해당 값은 반드시 false 여야 함
- **AssertTrue** 해당 값은 반드시 true 여야 함 
- **Email** 해당 값은Email 형식이어야 함
- **Max** 해당 값은 어노테이션에 정의된 값 이하여야 함
- **Min** 해당 값은 어노테이션에 정의된 값 이상이어햐 함
- **NotBlank** 해당 값은 null 이면 안되고, 빈값이어도 안되고 공백이어도 안됨 (" " 도 안됨)
- **NotEmpty** 해당 값은 null 이면 안되고, 빈값이어도 안됨(" " 허용)
- **NotNull** 해당 값은 null 이면 안됨


## 예제




## 기타

### 형변환 등과 같은 오류가 발생하는 경우

@Max(value = 11, message = "어쩌고저쩌고")

파라미터로 Integer 범위가 넘어가는, 예를 들어 1111111111 과 같은 값이 들어가면
Cast Error 가 발생함
이럴 때 해결 방안

### 디폴트 메시지 정의하기

MessageSource 를 Bean 으로 등록하여 메시지 처리를 공통 모듈화 시키는 방법



## 주의할 점

유효성 검사는 꼭 필요한 계층에서 꼭 필요한 데이터만으로 검증을 진행해야 한다.
검증 자체에 리소스가 소모되기 때문에 어플리케이션의 성능에 영향을 미칠 거라는 점을 항상 생각하면서 검증 절차를 추가해야 한다.

## Reference

[[SpringBoot] Spring Validation을 이용한 유효성 검증 - _koiil.log](https://velog.io/@_koiil/SpringBoot-Spring-Validation%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8%EC%84%B1-%EA%B2%80%EC%A6%9D)

[Spring Boot Bean Validation 제대로 알고 쓰자 - Continuous Growth](https://kapentaz.github.io/spring/Spring-Boo-Bean-Validation-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90/#)

[[Spring] Rest API Exception Handling - Dreamy 블로그](https://blog.naver.com/PostView.naver?blogId=writer0713&logNo=221605253778&parentCategoryNo=&categoryNo=83&viewDate=&isShowPopularPosts=true&from=search)

[[Spring] @Valid와 @Validated를 이용한 유효성 검증의 동작 원리 및 사용법 예시 - (1/2)망나니개발자](https://mangkyu.tistory.com/174)

[NHN Cloud - Validation 어디까지 해봤니?](https://meetup.toast.com/posts/223)