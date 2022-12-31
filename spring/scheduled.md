# Spring @Scheduled

> 스프링부트를 기준으로 함.

스프링부트에서 일정 기간, 혹은 특정 시간마다 코드를 실행시킬 수 있도록 설정하는 방법이다.

### @Scheduled 사용 방법

어플리케이션 클래스에 @EnableScheduling 어노테이션을 추가해준다.

```java
@EnableScheduling
@SpringBootApplication
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }

}
```

그리고 작업을 진행할 메서드에 ```@Scheduled``` 어노테이션을 추가해준다.
```@Scheduled``` 어노테이션이 추가된 클래스는 반드시 Spring Bean 으로 등록되어 있어야 한다.
(아래 예시에서는 @Service 어노테이션을 통해 MyService 클래스가 Spring Bean 객체임을 알 수 있다.)

또한 ```@Scheduled``` 어노테이션이 추가된 메소드는 **리턴 타입이 void 여야 하고 매개변수를 사용할 수 없다.**

```java
@Service
public class MyService {

    @Scheduled(fixedDelay = 1000)
    public void run() {
        System.out.println("Scheduled Run!!");
    }
}
```



### @Scheduled 속성

- fixedDelay(Integer) : 밀리세컨드(1/1000s) 단위로 이전 Task 의 **종료 시점**으로부터 정의된 시간만큼 지난 후 Task 를 실행한다.
- fixedDelayString(String) : fixedDelay 와 개념은 같으며 파라미터를 문자열로 입력한다.
- fixedRate(Integer) : 밀리세컨드(1/1000s) 단위로 이전 Task 의 **시작 시점**으로부터 정의된 시간만큼 지난 후 Task 를 실행한다.
- fixedRateString(String) : fixedRate 와 개념은 같으며 파라미터를 문자열로 입력한다.
- initialDelay(Integer) : 밀리세컨드(1/1000s) 단위로 초기 지연시간을 설정한다. 메서드가 최초에 등록 된 후 initialDelay 에서 정의한 시간 후에 첫 번째 Task 가 실행된다.
- initialDelayString(String) : initialDelay 와 개념은 같으며 파라미터를 문자열로 입력한다.
- cron : cron 표현식으로 작업을 예약한다.
  zone : cron 표현식을 사용했을 때 사용할 time zone으로 따로 설정하지 않으면 기본적으로 Local의 time zone이다.

> **@Scheduled 에서의 Cron 표현식**
> 
> ```@Scheduled(cron = "* * * * * *)```
> 
> 첫 번째 * 부터
> 
> 초(0-59)  
> 분(0-59)  
> 시간(0-23)  
> 일(1-31)  
> 월(1-12)  
> 요일(0-6) (0: 일, 1: 월, 2:화, 3:수, 4:목, 5:금, 6:토)  
> 
> **6자리 설정만 허용 가능하며 연도 설정을 할 수 없다.**
> 
> 참고사항
> ```
> * : 모든 조건(매시, 매일, 매주처럼 사용)을 의미
> ? : 설정 값 없음 (날짜와 요일에서만 사용 가능)
> - : 범위를 지정할 때
> , : 여러 값을 지정할 때
> / : 증분값, 즉 초기값과 증가치 설정에 사용
> L : 마지막 - 지정할 수 있는 범위의 마지막 값 설정 시 사용 (날짜와 요일에서만 사용 가능)
> ```


## Reference

https://dev-coco.tistory.com/176