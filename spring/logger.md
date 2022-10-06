# Logger

## Log 란?


## Log 종류


## Logging Level

``` FATAL > ERROR > WARN > INFO > DEBUG > TRACE```

- **FATAL**
  - 심각한 이슈, 프로그램 종료가 예상되는 이슈
  - 외부 API 에러일 경우 주로 사용
  - 예 : 네트워크 socket 바이딩 이슈, OAuth 외부 API 호출 에러

- **ERROR**
  - 프로그램 내에서 발생한 에러
  - 프로그램이 종료되는 등의 심각한 이슈가 발생하지는 않는 경우
  - 예 : 예외(Exception) 발생

- **WARN**
  - 잠재적인 이슈를 의미
  - 에러가 발생하기 전에 에러 가능성을 발견하기 위해 사용
  - 예 : in memory DB 가 가득차기 직전이거나, DB Connection 이 오래걸리는 경우

- **INFO**
  - 정보 제공을 목적으로 작성하는 로그
  - 예 : 시스템 동작 과정을 보여주는 경우

- **DEBUG**
  - 개발 환경에서 INFO 레벨보다 더 자세한 정보가 필요한 경우

- **TRACE**
  - DEBUG 레벨보다 더 자세한 정보가 필요한 경우
  - 성능 이슈로 인해 운영환경에서 사용하면 안된다.

## SpringBoot 에서의 Log

스프링에서는 Slf4j 라이브러리를 내장하고 있는데, Slf4j 는 디폴트 로거 구현 객체로 logback 프레임워크를 사용한다.

LoggerFactory.java , StaticLoggerBinder.java 확인

### 설정 방법



## Swagger 에서의 @Sl4fj



## 주의사항

괄호를 사용하지 않고 문자 더하기 연산으로 출력을 한다면 어떨까?
괄호 안의 문자 더하기 연산을 수행하고 로그 레벨을 확인하기 때문에 만일 로그레벨이 debug 이상이라면
연산만 수행하고 로그를 출력하지않는다.(불필요한 연산 발생)
그렇기에 괄호를 사용한 로그 출력을 사용해야한다. 


## Reference

https://d-memory.tistory.com/31
https://clack2933.tistory.com/34