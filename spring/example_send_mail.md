# Spring 에서 메일 보내는 예제 소스코드

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

원인은 아래 소스코드에서 찾았다.

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


## html 불러오기

```javax.mail.internet.MimeMessage``` 클래스를 이용해 HTML 템플릿을 메일로 보내는 예제 소스코드이다.

```java

@Getter
public class MailInfo {

    //수신자 메일
    private String[] recipients;

    //메일 제목
    private String subject;

    //메일 내용
    private String body;

    //수신자가 1명일 경우의 생성자
    public MailInfo(String recipient, String subject, String body) {
        this.recipients = new String[]{recipient};
        this.subject = subject;
        this.body = body;
    }

    //수신자가 여러 명일 경우의 생성자
    public MailInfo(String[] recipients, String subject, String body) {
        this.recipients = recipients;
        this.subject = subject;
        this.body = body;
    }
}

@RequiredArgsConstructor
@Component
@Slf4j
public class CustomMailSender {

    @Value("${spring.mail.username}")
    private String from;

    private final JavaMailSender javaMailSender;


    public void send(MailInfo mailInfo) {
        log.info(" :: Send mail start :: ");
        log.info(" ==== Mail info is ==== ");
        log.info("  => Recipient : " + mailInfo.getRecipients());
        log.info("  => From : " + fromUsername);

        MimeMessage mimeMessage = javaMailSender.createMimeMessage();

        try {
            for (String recipient : mailInfo.getRecipients()) {
                mimeMessage.addRecipients(Message.RecipientType.TO, recipient);
            }

            mimeMessage.setFrom(fromUsername);
            mimeMessage.setSubject(mailInfo.getSubject());
            mimeMessage.setText(mailInfo.getBody());
            mimeMessage.setText(mailInfo.getBody(), "utf-8", "html");
        } catch (MessagingException e) {
            e.printStackTrace();
            return;
        } catch (MailException e) {
            e.printStackTrace();
            return;
        } catch (Exception e) {
            e.printStackTrace();
            return;
        }

        javaMailSender.send(mimeMessage);
    }
}
```

```java

@RequiredArgsConstructor
public class CustomService {

    private final CustomMailSender CustomMailSender;

    public void send() {
        String htmlTemplate = this.readHtmlTemplate("html_template");
        MailInfo mailInfo = new MailInfo("수신자 이메일", "제목", htmlTemplate);
        
        CustomMailSender.send(mailInfo);
    }

    private String readHtmlTemplate(String fileName) throws ApiException {
        Resource resource = resourceLoader.getResource("classpath:template/mail/" + fileName + ".html");
        try (
                InputStream inputStream = resource.getInputStream();
                BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream))
        ) {
            StringBuilder stringBuilder = new StringBuilder();
            String line;
            while ((line = reader.readLine()) != null) {
                stringBuilder.append(line);
            }

            reader.close();
            return stringBuilder.toString();
        } catch (IOException e) {
            e.printStackTrace();
        }

        throw new ApiException(CodeType.ERROR_INTERNAL);
    }
}

```
