# Spring Bean Life Cycle

동적으로 커넥션 객체 만들고 특정 시간이 지나면 소멸하는 Bean 객체 만들어 관리하는 로직을 만들어야 함.
해당 로직 만들기 전에 Spring Bean 의 라이프사이클을 정리하고, 어떤 방법으로 적용할 지 공부하기 위함




### Life Cycle

```
1. 스프링 컨테이너 생성
2. 스프링 빈 생성
3. 의존관계 주입
4. 초기화 콜백
5. 사용
6. 소멸 전 콜백
7. 스프링 종료
```

### Life Cycle Callback

스프링 빈은 크게 3가지 방법으로 빈 생명주기 콜백을 지원한다.

- 인터페이스(InitializingBean, DisposableBean)
- 설정 정보에 초기화 메소드, 종료 메소드 지정
- **@PostConstruct, @PreDestroy 애노테이션 지원**



## Reference

https://devlog-wjdrbs96.tistory.com/321