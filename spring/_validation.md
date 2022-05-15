# Spring Validation

클라이언트에서도 서버로 보낼 데이터들의 유효성을 검사하겠지만, 안전한 통신을 위해서는 서버에서도 데이터의 유효성을 검증할 필요가 있다.

> **유효성 검증**
> 
> 어떠한 객체 등의 데이터 값이 로직에 맞는 알맞은 값이 들어가 있는지, 필수 값이 누락되어 있지는 않은지 등을 확인하는 절차를 말한다.

JAVA 에서는 


Bean Validation 은 자바에서 유효성 검사를 하는 방법에 대한 명세이며, 대표적인 구현체로는 Hibernate Validator 가 있다.
그리고 SpringBoot2.0 이상부터는 Bean Validation2.0 을 사용하고 있다.

@Valid

- JAVA 표준 유효성 검증 어노테이션
- DispatcherServlet > ArgumentResolver 에서 유효성 검증이 진행된다.


@Validated

- Spring Framework 에서 제공하는 유효성 검증 어노테이션
- AOP 를 기반으로 유효성 검증이 진행된다.


## 예제





## Reference

[[SpringBoot] Spring Validation을 이용한 유효성 검증 - _koiil.log](https://velog.io/@_koiil/SpringBoot-Spring-Validation%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8%EC%84%B1-%EA%B2%80%EC%A6%9D)

[ㅁㅁ](https://kapentaz.github.io/spring/Spring-Boo-Bean-Validation-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90/#)

[ㄴㄴ](https://blog.naver.com/PostView.naver?blogId=writer0713&logNo=221605253778&parentCategoryNo=&categoryNo=83&viewDate=&isShowPopularPosts=true&from=search)

[망나니개발자](https://mangkyu.tistory.com/174)

[NHN Cloud - Validation 어디까지 해봤니?](https://meetup.toast.com/posts/223)