# Logger

## Log 란?



## Log 종류

### Apache Log4j

Apache Log4j는 굉장히 오래된 로그 프레임워크이다. 계층 로그 레벨과 로거같은 기본 컨셉을 가지고 있으며, 현대 로깅 프레임워크의 기반이 되었다. 
하지만, 2015년 서비스 종료를 알렸기 때문에, 레거시가 아닌 이상 새로운 프로젝트는 다른 프레임워크를 사용해야 한다.

### Logback

Logback은 Log4j와 같은 개발팀이 만들었다. Log4j와 기본 컨셉은 똑같지만, 성능 향상이 되었다. 
또한 SLF4J를 기본적으로 지원하며 향상된 필터 옵션이나 로그 설정 자동 새로고침등의 기능도 구현한다. 
현재 spring-boot-starter-web의 기본 로깅 프레임워크이다.

### Apache Log4j2

Apache Log4j2는 3가지 로깅 프레임워크 중 가장 최신이다. 목표는 Log4j와 Logback의 문제점을 개선하고 Log4j 성능 향상이다. 
Lockback과 마찬가지로 Log4j2는 SLF4J를 기본적으로 지원하고, 로깅 설정을 자동 새로고침, 향상된 필터 옵션을 지원을 한다. 
게다가 람다식에 기반하여 로그 상태의 lazy evaluation 지원, low-latency를 위한 비동기 로깅제공, 
GC 작동에 의해 발생하는 지연을 피하기위한 garbage-free mode도 제공한다.

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

https://escapefromcoding.tistory.com/554