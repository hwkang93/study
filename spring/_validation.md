# Spring Validation

클라이언트에서도 서버로 보낼 데이터들의 유효성을 검사하겠지만, 안전한 통신을 위해서는 서버에서도 데이터의 유효성을 검증할 필요가 있다.
JAVA 에서는 기본적으로 

@Valid

- JAVA 표준 유효성 검증 어노테이션
- DispatcherServlet > ArgumentResolver 에서 유효성 검증이 진행된다.
- 유효성 검증이 실패하면 MethodArgumentNotValidException 예외가 발생하며 400 코드를 리턴한다.
- 이 요청은 컨트롤러로 진입하기 전에 프론트 컨트롤러에서 발생한 에러이므로 서버 에러코드(500)를 리턴하지 않는다.

@Validated

- Spring Framework 에서 제공하는 유효성 검증 어노테이션
- AOP 를 기반으로 유효성 검증이 진행된다.
- 유효성 검증이 실패하면 ConstraintViolationException 예외가 발생하며 별다른 처리가 없을 시 500 코드를 리턴한다.
- 이 요청은 AOP 를 기반으로 메소드 호출 전에 유효성 검증 메소드에서 에러가 발생한 것이므로 서버 에러코드를 리턴한다.

## 예제


메시지 처리


## Reference

[[SpringBoot] Spring Validation을 이용한 유효성 검증 - _koiil.log](https://velog.io/@_koiil/SpringBoot-Spring-Validation%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8%EC%84%B1-%EA%B2%80%EC%A6%9D)

[ㅁㅁ](https://kapentaz.github.io/spring/Spring-Boo-Bean-Validation-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90/#)

[ㄴㄴ](https://blog.naver.com/PostView.naver?blogId=writer0713&logNo=221605253778&parentCategoryNo=&categoryNo=83&viewDate=&isShowPopularPosts=true&from=search)

[망나니개발자](https://mangkyu.tistory.com/174)