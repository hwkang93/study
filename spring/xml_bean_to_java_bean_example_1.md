# xml 로 설정된 spring bean 을 java 에서 설정하는 예제


context.xml 에 설정된 다음 bean 이 있다.

```context.xml```

```xml
<bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="locations">
        <list>
            <value>classpath:/egovframework/egovProps/${spring.profiles.active}.properties</value>
        </list>
    </property>
</bean>
```

여기에 추가 요구사항이 생긴다.

> 프로젝트 외부에서 properties 파일을 참조할 수 있게 해주세요.   
> 파일을 참조할 수 없는 경우(디폴트) 기존 프로젝트의 프로퍼티 파일을 참조해야 해요.

java 로 정의된 bean 과는 다르게 xml 로 정의된 bean 객체는 동적으로 값을 할당할 수 없는 걸로 알고 있다.
그렇기 때문에 xml 로 정의된 ```propertyConfigurer```를 java bean 으로 바꿔보았다. 

```org.springframework.beans.factory.config.PropertyPlaceholderConfigurer``` 클래스를 확장한 CustomPropertyPlaceholderConfigurer.java 파일을 만들고,
간단히 생성자만 재정의해줬다.

```java
@Slf4j
public class CustomPropertyPlaceholderConfigurer extends PropertyPlaceholderConfigurer {

    public CustomPropertyPlaceholderConfigurer() {
        super();

        String springProfileActive = System.getProperty("spring.profiles.active");
        Resource defaultLocation = new ClassPathResource("/egovframework/egovProps/" + springProfileActive + ".properties");
        super.setLocations(defaultLocation);

        try {
        	String geonProfilesLocation = "file:" + System.getProperty("my.profiles.location");
        	log.info("외부에 정의된 properties 파일을 참조합니다. location is :: " + geonProfilesLocation);
            Resource resource = new UrlResource(geonProfilesLocation);
            if(resource.exists()) {
                log.info(" location exists");
                super.setLocations(resource);
            } else {
            	log.info(" location not exists");
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

PropertyPlaceholderConfigurer 클래스의 Resource[] 는 가장 마지막에 추가된 Resource 객체를 우선으로 하기 때문에,
defaultLocation (기존 xml 에 정의된 내용)을 먼저 정의하고, 환경변수로 선언된 경로(my.profiles.location)가 존재하면, 해당 경로를 바라보도록 했다.

context.xml 은 다음과 같이 바꿔주면 된다.

```context.xml```

```xml
<bean id="propertyConfigurer" class="my.cmm.config.CustomPropertyPlaceholderConfigurer"/>
```

환경변수는 다음과 같이 설정할 수 있는데, 

> -Dmy.profiles.location=D:\my.properties

서버의 환경에 따라 톰캣에 선언할 수도 있고, 도커나 쿠버네티스 기반이라면 각 서버의 설정 파일에 선언할 수 있다.