# Logger

## Log 란?

프로그램을 개발 또는 운영하고 있을 때, 프로그램 실행 상태에 대한 정보를 출력하고 기록하는 것을 말한다.

## Log 프레임워크 종류

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

> **Slf4j(Simple Logging Facade for Java)**   
> 
> 로깅 프레임워크에 대한 추상화 라이브러리. 
> slf4j 를 통해 로깅 프레임워크 구현체의 종류와 상관없이 일관된 로깅 코드를 작성할 수 있다.

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

-----------------------------------

## 지양해야 할 로그 작성법

```java.lang.System.out``` ```java.lang.System.err``` 을 사용해 로그를 기록하는 것은 좋은 습관이 아니다.
```e.printStackTrace()``` 는 내부적으로 ```java.lang.System.err``` 클래스를 사용한다.

```java
System.out.println("사용자 정보 : " + userId);
```

```java
try {
    //...
} catch (Exception e) {
	e.printStackTrace();
}

```

```java.lang.System``` 을 사용해 로그를 기록하는 것이 왜 안좋은 습관인지 먼저 확인해보자.

### 휘발성

```System.out.println``` 은 로그가 표준 출력으로 출력된다. 파일로 저장되지 않고 휘발된다는 의미이다.
로그의 목적 중 하나는 에러가 발생한 상황을 기록하고, 기록을 통해 문제를 진단하고, 해결하기 위함인데
저장되지 않는 정보는 로그로써의 목적을 잃었다고 할 수 있다.

### 로그 레벨 관리 불가

로컬에서 개발할 때에는 디버깅을 위한 아주 상세한 정보가 출력되어 확인할 수 있어야 한다.
하지만, 프로덕션에서 동작하는 코드는 에러/장애가 발생할 때 문제를 진단할 수 있는 정보만을 남겨야 한다.
개발시에만 사용되는 정보와 문제 상황에 대한 정보가 함께 로깅된다면 문제 해결을 위한 정작 중요한 정보를 얻기 힘들 뿐더러, 민감한 정보를 로그로 남길수도 있기 때문이다.
또한 의미없는 로그가 쌓여 서버 용량을 차지할 수도 있다.   
따라서 로깅 라이브러리는 환경에 맞게(로컬 개발 환경, 개발 서버, 프로덕션 서버 등) 로그가 출력될 수 있도록 로그 출력 레벨이라는 기능을 제공한다.
많이 사용되는 Logback이라는 라이브러리에서는 TRACE, DEBUG, INFO, WARN, ERROR, FATAL 와 같은 레벨을 제공한다.
하지만 System.out.println() 은 이런 기능을 제공하지 않는다.
어떤 환경에서든 동일한 로그가 출력된다. 프로덕션에서 이런 로그를 제거하려면 코드를 일일히 제거하거나 주석처리하거나 별도의 조건문을 설정하는 등 번거로운 일들을 해야한다.

### 성능 저하 가능성
다음은 ```System.out.println()``` 의 코드이다.
```java
/**
* Terminates the current line by writing the line separator string.  The
* line separator string is defined by the system property
* {@code line.separator}, and is not necessarily a single newline
* character ({@code '\n'}).
  */
public void println() {
  newLine();
}
```


```println()``` 은 ```newLine()``` 을 호출한다.   
다음은 ```newLine()``` 코드이다.

```java
private void newLine() {
    try {
        synchronized (this) {
            ensureOpen();
            textOut.newLine();
            // ...
```

내부적으로 ```synchronized``` 를 사용한다. 이때 newLine() 메소드는 임계영역(critical section)이 된다.
멀티 쓰레드 환경에서 A 쓰레드가 newLine() 메소드를 실행하면, 메소드는 잠기게 된다.
다른 쓰레드는 A 쓰레드가 모두 사용하고 잠금을 풀어준 뒤에서야 newLine() 메소드를 실행할 수 있다. 
**오버헤드가 발생**하게 되는 것이다.

스프링을 실행하는 톰캣은 멀티 쓰레드로 동작한다. 요청이 오면 쓰레드 풀에서 쓰레드를 하나 가져와 요청을 처리한다.
그런데, ```System.out.println()``` 을 여러 쓰레드가 사용하면 그만큼 오버헤드가 발생하고 처리가 느려질 것이다.


## 로그 프레임워크 사용

```System.out``` 을 사용해 로그를 출력하는 것은 로그로써의 목적을 위해서나 관리의 편의성을 위해서나 성능을 위해서나 지양해야 한다.
그리고 logback 프레임워크나, log4j 와 같은 로그 프레임워크 사용을 권장한다. 다음은 로그 프레임워크를 사용해 올바른 로그를 작성하는 방법이다.

### 하나의 파라미터가 있는 경우

아래 4개의 방법은 모두 동일한 결과를 출력한다. 그리고 INFO, WARN, ERROR 로그 레벨 처럼 DEBUG 보다 상위 레벨일 경우
4개 방법 모두 출력이 일어나지 않는다. 그럼에도 불구하고 어떤 경우는 성능에 부정적인 영향을 미친다.

```java
private void bindOneParameter(String userName) {

    // poor performance, poor readability
    logger.debug("Hello " + userName + ".");

    // always good performance, poor readability
    if(logger.isDebugEnabled()) {
        logger.debug("Hello " + userName + ".");
    }

    // always good performance, good readability
    if(logger.isDebugEnabled()) {
        logger.debug("Hello {}.", userName);
    }

    // good performance, best readability - I recommend this.
    logger.debug("Hello {}.", userName);
}
```

#### 문자열 직접 연산 방법

```java
// poor performance, poor readability
logger.debug("Hello " + userName + ".");
```

가용 로그 레벨을 DEBUG 에서 INFO 로 조절하면 로그가 남는 과정이 생략되기 때문에 성능 개선을 할 수 있다.
하지만 ```logger.debug()``` 메소드가 실행되기 전에 ```"Hello " + userName + "."``` 이라는 문자열 연산이 먼저 일어나기 때문에
문자열 연산 만큼의 성능 악화가 발생한다.   
```+``` 와 같은 문자열 연산만의 문제가 아니라 ```logger.debug()``` 메소드 실행 이전에 어떠한 연산이라도 일어나게 되면 동일한 낭비가 발생하게 된다.

#### 가용 로그 레벨 체크

```java
// always good performance, poor readability
if(logger.isDebugEnabled()) {
    logger.debug("Hello " + userName + ".");
}
```

가용 로그 레벨이 INFO 라면, ```logger.debug()``` 메소드가 실행되지 않을 뿐만 아니라 문자열 연산도 일어나지 않기 때문에 성능 낭비가 없다.

#### 가용 로그 레벨 체크 & SLF4J 치환문자 사용

```java
// always good performance, good readability
if(logger.isDebugEnabled()) {
    logger.debug("Hello {}.", userName);
}
```

두번째 방법과 동일하게 성능 낭비가 없으며, SLF4J 의 치환문자 ```{}``` 를 이용해 가독성도 높일 수 있다.

#### SLF4J 치환문자 사용

```java
// good performance, best readability - I recommend this.
logger.debug("Hello {}.", userName);
```

먼저 첫번째 방법과 비교하면 ```logger.debug()``` 메소드가 실행되기 전에 ```+``` 와 같은 연산은 없어서 성능 개선이 이루어졌으며 로그의 가독성도 올라갔다.

세 번째 방법과 비교하면 ```"Hello {}."``` 문자열 생성과 같은 연산이 발생하기 때문에 성능이 약간 떨어진다.

그리고 ```logger.debug()``` 메소드는 실행되지만 해당 메소드 내에서 가장 먼저 가용 로그 레벨을 체크하는 과정이 가장 먼저 일어나기 때문에 
```Object.toString()```과 같은 추가적인 연산은 발생하지 않는다.



## Reference

https://d-memory.tistory.com/31

https://clack2933.tistory.com/34

https://escapefromcoding.tistory.com/554

https://hudi.blog/do-not-use-system-out-println-for-logging/

http://dveamer.github.io/backend/HowToUseSlf4j.html