# Spring Validation

클라이언트에서도 서버로 보낼 데이터들의 유효성을 검사하겠지만, 안전한 통신을 위해서는 서버에서도 데이터의 유효성을 검증할 필요가 있다.

@Valid

- JAVA 표준 유효성 검증 어노테이션
- 제약 조건이 부여된 객체에 Bean Validator 를 이용해서 검증하도록 지시한다.
- Bean Validator 의 대표적인 구현체로 Hibernate Validator 가 있다.
  - 스프링부트에서 디폴트 구현체로 사용된다.
- DispatcherServlet > ArgumentResolver 에서 유효성 검증이 진행된다.
- 프론트 컨트롤러인 DispatcherServlet 에서 유효성을 검증하기 때문에 예외가 발생하면 400 에러를 리턴한다. (MethodArgumentNotValidException)
- 위와 같은 이유로 @Valid 는 컨트롤러에서만 동작한다.

@Validated

- Spring Framework 에서 제공하는 유효성 검증 어노테이션
- AOP 를 기반으로 유효성 검증이 진행된다.
- 예외 발생 시 500 에러가 발생한다. (ConstraintViolationException)
- 컨트롤러 이외의 다른 계층에서 사용이 가능하다.



## 주의할 점

유효성 검사는 꼭 필요한 계층에서 꼭 필요한 데이터만으로 검증을 진행해야 한다.
검증 자체에 리소스가 소모되기 때문에 어플리케이션의 성능에 영향을


## 예제





## Reference

[[SpringBoot] Spring Validation을 이용한 유효성 검증 - _koiil.log](https://velog.io/@_koiil/SpringBoot-Spring-Validation%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8%EC%84%B1-%EA%B2%80%EC%A6%9D)

[Spring Boot Bean Validation 제대로 알고 쓰자 - Continuous Growth](https://kapentaz.github.io/spring/Spring-Boo-Bean-Validation-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90/#)

[[Spring] Rest API Exception Handling - Dreamy 블로그](https://blog.naver.com/PostView.naver?blogId=writer0713&logNo=221605253778&parentCategoryNo=&categoryNo=83&viewDate=&isShowPopularPosts=true&from=search)

[[Spring] @Valid와 @Validated를 이용한 유효성 검증의 동작 원리 및 사용법 예시 - (1/2)망나니개발자](https://mangkyu.tistory.com/174)

[NHN Cloud - Validation 어디까지 해봤니?](https://meetup.toast.com/posts/223)