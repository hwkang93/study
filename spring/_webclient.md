# WebClient

Spring 어플리케이션에서 API 요청을 해야 하는 경우가 있는데, 이런 경우를 위해 Spring 진영에서는 RestTemplate 와 WebClient 를 제공하고 있다.

## 정리하기에 앞서..

WebClient 가 등장하게 된 배경을 이해하려면 Spring 3.0 부터 제공하고 있는 RestTemplate 에 대한 이해가 필요하다.

RestTemplate 는 HTTP 요청 방식이 멀티 쓰레드 + 동기 방식이다.

RestTemplate 의 요청 과정을 살펴보면,

1. 요청은 먼저 큐에 쌓인다.
2. 큐에서는 쓰레드 중 하나에 HTTP 요청을 할당하여 처리한다.
3. 쓰레드는 HTTP 요청을 전송하고, 요청에 대한 응답이 올 때까지 대기한다.
4. 응답이 도착하면 응답을 리턴하고 쓰레드를 반환한다.

RestTemplate 는 멀티 쓰레드 방식을 사용하기 때문에, 여러 요청을 동시에 처리할 수 있다.
또한 동기 방식이기 때문에 요청에 대한 응답이 도착할 때까지 다른 요청을 처리하지 못하고 대기하게 된다.

만약 사용 가능한 쓰레드가 모두 응답을 대기하고 있는 상태라면, 
그 이후부터는 응답이 도착할 때까지 클라이언트의 요청은 대기 상태에 머무르게 된다.

> **RestTemplate.java**
> 
> NOTE: As of 5.0 this class is in maintenance mode, with only minor requests for changes and bugs to be accepted going forward. 
> Please, consider using the org.springframework.web.reactive.client.WebClient which has a more modern API and supports sync, async, and streaming scenarios.


다음은 RestTemplate 클래스의 주석의 일부이다. 
Spring 5.0 기준으로 RestTemplate 는 더 이상의 업데이트가 없을 것이라고 명시되어 있으며, RestTemplate 보다 WebClient 를 권장한다.



## WebClient 란?

Spring WebFlux 에서 제공하는 HTTP 요청에 대한 인터페이스로, Spring 5.0 부터 추가되어 제공하고 있다.

WebClient 는 Multi Thread + Blocking 방식을 사용하는 RestTemplate 와는 다르게,
Single Thread 와 Non-Blocking 방식을 사용한다.

1. 클라이언트에서 요청이 들어오면 Event Loop 내의 Job 으로 등록된다.
2. 등록된 Job 은 쓰레드에 할당되어 HTTP 요청을 전송한다.
3. 쓰레드는 요청을 보낸 후 응답을 기다리지 않고(Non-Blocking) 자원을 반환한다.
4. Event Loop 는 해당 Job 에 대한 응답이 도착하면 그 결과를 클라이언트에게 리턴한다.

### Mono VS Flux

Spring Webflux 에서 사용하는 reactive library 가 Reactor 이고 Reactor 가 Reactive Streams 의 구현체이다.

그래서 Webflux 문서에 Reactive Streams 가 언급되는 것이고 그것과 같이 Reactor 가 나오고

주요 객체인 Mono / Flux 가 나오는 것이다. 결국 Webflux 의 동작 구조를 이해하는 Flux 와 Mono 를 알고 넘어가야 한다.

Flux 와 Mono 의 차이점은 발행하는 데이터 갯수이다.

#### Flux
- 0-N 개의 데이터를 처리한다.
- 하나의 데이터를 전달할 때마다 onNext 이벤트를 발생시킨다.
- 모든 데이터의 전달이 정상적으로 완료되면, onComplete 이벤트를 발생시킨다.
- 데이터 전달 중 오류가 발생하면 onError 이벤트가 발생한다.

#### Mono
- O-1 개의 데이터를 처리한다.







## 실습

WebClient 객체 는 정적 팩토리 메소드로 만들 수 있다.

## 마치며

기존에 주를 이루던 멀티 쓰레드 + 동기 방식은 모든 소스코드가 순차적으로 진행되어, 에러 발생 시 에러를 추적(stacktrace)하기가 쉬웠다.
하지만 논블로킹 방식의 프로그램은 디버깅하기가 상당히 어렵다는 걸 느꼈다.
오류가 발생해도, 정확한 오류의 위치를 찾는 게 stacktrace 만으로는 한계가 있다는 걸 느꼈고,
열심히 구글링을 해 본 결과 다른 사람들도 비슷한 느낌을 받는다는 걸 알게 되었다.

조만간 디버깅을 쉽게 할 수 있는 방법에 대해 좀 알아봐야겠다.


## Reference

https://docs.spring.io/spring-framework/docs/5.2.6.RELEASE/spring-framework-reference/web-reactive.html#webflux-client

https://tecoble.techcourse.co.kr/post/2021-07-25-resttemplate-webclient/

https://happycloud-lee.tistory.com/220

https://doinge-coding.tistory.com/entry/spring-webClient%EC%82%AC%EC%9A%A9%EB%B0%A9%EB%B2%95

https://medium.com/@odysseymoon/spring-webclient-%EC%82%AC%EC%9A%A9%EB%B2%95-5f92d295edc0

https://devuna.tistory.com/120