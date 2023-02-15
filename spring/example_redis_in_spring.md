# SpringBoot 환경에서 Redis 사용하는 예제

SpringBoot 에서 Redis 를 사용하는 예제 소스코드이다. 매우 간단하고 쉽게 사용해볼 수 있다.


### 예제

먼저 pom.xml 에 redis 라이브러리를 추가해준다.

> SpringBoot 와 Lombok 라이브러리도 함께 사용했다.

#### pom.xml

```xml
<!-- redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

다음으로 application.properties 파일에 redis 의 접속 정보를 입력한다.

#### application.properties

```properties
spring.redis.host=localhost
spring.redis.port=6379
```

다음은 java 설정 파일이다.

#### RedisConfig.java

```java
@Configuration
public class RedisConfig {

    @Value("${spring.redis.host}")
    private String host;

    @Value("${spring.redis.port}")
    private int port;

    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        LettuceConnectionFactory lettuceConnectionFactory = new LettuceConnectionFactory(host, port);
        return lettuceConnectionFactory;
    }

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(redisConnectionFactory);

        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(new StringRedisSerializer());

        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new StringRedisSerializer());

        return redisTemplate;
    }
}
```

다음은 Entity 를 정의한다. 예제의 경우 Refresh Token 객체를 정의했다.

#### RefreshToken

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.Getter;
import org.springframework.data.annotation.Id;
import org.springframework.data.redis.core.RedisHash;

@Getter
@AllArgsConstructor
@Data
@RedisHash("refreshToken")
public class RefreshToken {

    @Id
    private String key;

    private String value;

    public RefreshToken updateValue(String token) {
        this.value = value;
        return this;
    }
}
```


다음은 Repository 를 정의한다. JPA 와 유사한 형태이다.

#### RefreshTokenRepository

```java
import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface RefreshTokenRepository extends CrudRepository<RefreshToken, String> {
    
}
```