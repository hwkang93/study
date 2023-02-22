# Spring 에서 메일 보내는 예제 소스코드

> feat. 회사메일

구글에 검색해보면 Google SMTP 를 사용해 발신자가 구글 계정인 예제 소스코드들을 바로 만나볼 수 있다.
소스코드의 양, 의존성도 많지 않아 쉽게 메일 실습을 해볼 수 있다.

그럼에도 불구하고 글을 작성하는 이유는 밑에 작성할 **오류** 해결에 애를 먹었기 때문이다.

우선 예제 소스코드를 확인해보자.

## 준비

테스트를 위해 스프링부트 프로젝트를 하나 만들어서 진행했다.

플로우는 다음과 같다.

1. 스프링부트 프로젝트 실행
2. 프로젝트 실행 완료 후 CommandLineRunner 인터페이스의 run 메서드를 구현하고, MailService 의 sendMail 메소드를 실행
3. 프로젝트 종료

pom.xml 에 SpringBoot-Web, SpringBoot-Mail, Lombok 의존성을 추가해준다.

#### pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- SpringBoot mailService -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

application.yaml 에 메일에 사용할 프로퍼티들도 정의해준다. 내 경우 잘 정리된 사내 문서가 있어 참고했다.

#### application.yaml 

````yaml
spring:
  mail:
    host: [회사 SMTP 서버]
    port: [회사 SMTP 서버 포트]
    username: [내 회사 메일]
    password: [내 회사 메일 비밀번호]
    properties:
      mail.smtp.ssl.enable: true
      mail.smtp.auth: true
````



SpringBootApplication 클래스를 구현해준다.

#### MailSampleApplication.java

```java
@RequiredArgsConstructor
@SpringBootApplication
public class MailSampleApplication implements CommandLineRunner {

	private final ApplicationContext applicationContext;

	public static void main(String[] args) {
		SpringApplication.run(MailSampleApplication.class, args);
	}

	@Override
	public void run(String... args) throws Exception {
		MailService mailService = applicationContext.getBean(MailService.class);
		mailService.sendMail();

		System.exit(-1);
	}
}
```

위에 작성했던 플로우로 구성되어 있는 클래스이다.



## 오류

구글 계정, 구글 서버로 작성한 예제는 별 문제없이 잘 성공했으나, 회사 SMTP 서버 및 회사 메일로 변경하고 소스코드를 실행해보니 다음 에러가 발생했다.

> com.sun.mail.smtp.SMTPSendFailedException: 553 5.1.8 Requested action not taken: mail box name <KHW@DESKTOP-NMJM4U7> not allowed.

저녁 내 에러 코드만 보고 구글링을 해봤는데, 별 소득 없이 소중한 저녁이 지나가버렸다.

다음날 아침, 이 에러 메시지로는 구글링을 아무리 해도 의미가 없을 것이라고 판단하고 코드 내에서 원인을 찾아보기로 했다.
그리고 얼마 지나지 않아 디버깅을 하다 보니 원인을 금방 찾을 수 있었다.

#### javax.mail.internet.InternetAddress

```java
static InternetAddress _getLocalAddress(Session session) throws SecurityException, AddressException, UnknownHostException {
    String user = null;
    String host = null;
    String address = null;
    if (session == null) {
        user = System.getProperty("user.name");
        host = getLocalHostName();
    } else {
        address = session.getProperty("mail.from");
        if (address == null) {
            user = session.getProperty("mail.user");
            if (user == null || user.length() == 0) {
                user = session.getProperty("user.name");
            }

            if (user == null || user.length() == 0) {
                user = System.getProperty("user.name");
            }

            host = session.getProperty("mail.host");
            if (host == null || host.length() == 0) {
                host = getLocalHostName();
            }
        }
    }

    if (address == null && user != null && user.length() != 0 && host != null && host.length() != 0) {
        address = MimeUtility.quote(user.trim(), "()<>,;:\\\"[]@\t ") + "@" + host;
    }

    return address == null ? null : new InternetAddress(address);
}
```

위 함수는 발신자의 메일을 찾는 함수이다. 이 함수에서 알 수 있듯이 발신자의 메일을 못찾으면 (```session.getProperty("mail.from")```)
시스템 환경변수에 정의된 내 로컬 컴퓨터 명칭(```System.getProperty("user.name")```)이 발신자가 된다.

MailService.java 클래스에 발신자를 할당하는 라인 한 줄을 추가해준다.

```java
simpleMailMessage.setFrom("발신자 메일");
```

### 느낀점

사실 이번 글을 쓴 이유이기도 하다.

1. 함수 명, 함수 내용을 보고 직관적인 소스코드 이해가 가능해야 한다.

원인을 찾으면서 느끼는 거였는데, 빠른 시간 내에 문제를 해결할 수 있었던 이유 가장 첫 번째가 개발자 누가 봐도 직관적이고 잘 짜여진 소스코드라는 것이었다.
5년 넘게 코딩을 하면서 늘 고민하고, 노력해도 잘 안느껴지는게 깔끔한 코딩이다. 
이번에 디버깅을 하면서 코드의 깔끔함이 얼마나 중요한지 다시 느꼈다. 
이건 다음에 다시 코드를 들여다 볼 미래의 나에 대한 예의이며 언젠가 인수인계해야 할 상황의 후임자에 대한 배려이다.

2. 구글링은 오류 찾을 때 쓰는 게 아니다.





## html 불러오기



