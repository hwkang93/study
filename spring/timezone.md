# Spring Boot Application Timezone 확인

스프링부트로 개발한 어플리케이션을 개발서버에 (쿠버네티스-도커 환경)올리는 일이 있었다.

로그에 찍히는 시간, java의 LocalDateTime 객체의 TimeZone 이 한국 시간대가 아니었다.

1. 쿠버네티스 TimeZone 확인하기
2. 도커 TimeZone 확인하기

모두 KST 로 정상적인 한국 시간대를 사용하고 있었다.

### TimeZone 확인

어플리케이션에서 어떤 시간대를 사용하고 있는지 확인하는 방법에는 Application.java 에 다음 소스코드를 추가하는 방법이 있다.

```java
@SpringBootApplication
@EnableJpaAuditing
@Slf4j
public class PortalCoreApplication {

    @PostConstruct
    public void started() {
        String displayName = TimeZone.getDefault().getDisplayName();
        String id = TimeZone.getDefault().getID();
        log.info("== Application TimeZone :" + id + "   " + displayName + " ==");

    }

    public static void main(String[] args) {
        SpringApplication.run(PortalCoreApplication.class, args);
    }

}
```

로그 내용은 다음과 같다.

```
2023-04-26 04:31:34.897  INFO 1 --- [           main] org.test.MyApplication        : == Application TimeZone :Etc/UTC   Coordinated Universal Time ==
```

### 서버에서 변경하기 (linux)

java 의 TimeZone 객체는 디폴트로 /etc/timezone 을 참조하기 때문에 /etc/timezone 을 Etc/UTC 에서 Asia/Seoul 로 변경해야 한다.

```bash
sudo vi /etc/timezone
```

```
-- AS-IS
Etc/UTC

-- TO-BE
Asia/Seoul
```

### k8s yml 파일에서 변경하기

Docker image의 default timezone은 UTC이다. 결국 log도 UTC로 남아 로그를 분석할 때 좋지 않다. 
-Duser.timezone=Asia/Seoul 설정으로 JVM의 timezone을 한국시간으로 변경할 수 있다

```yaml
containers:
      - name: main-server
        image: main-server:latest
        env:
          - name: SPRING_PROFILES_ACTIVE
            value: develop
          - name: JAVA_TOOL_OPTIONS
            value: "-Duser.timezone=Asia/Seoul"
        ports:
        - containerPort: 8080
```

### Applicaiton.java 파일에서 수정하기

```java
TimeZone.setDefault(TimeZone.getTimeZone("Asia/Seoul"));
```

위 소스코드를 Application.java 에 추가하는 방법도 있다.


## Reference

https://velog.io/@ashappyasikonw/Java-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EC%97%90%EC%84%9C-Default-Time-Zone%EC%9D%80-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%84%A4%EC%A0%95%EB%90%98%EB%8A%94%EA%B0%80