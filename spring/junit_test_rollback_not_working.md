
최근 운영하던 API 서비스들의 테스트케이스를 작성하기로 하면서 있었던 일이다. 해결하는 데 거의 하루를 꼬박 보냈다.

## 문제의 시작

> 테스트 케이스에 @Transactional 어노테이션을 사용해도 테스트 후 롤백이 안돼요.

## 의심할만한 정황

진행중인 개인 프로젝트와 비슷한 방식으로 샘플 테스트 케이스를 작성했다.
연습을 위해 만들었던 개인 프로젝트의 테스트는 별 문제가 없었기 때문에 두 개의 프로젝트를 비교하면서 의심가는 부분을 찾아보았다.


### 1. @SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)

@SpringBootTest 의 WebEnvironment 값을 디폴트(MOCK) 값이 아닌 RANDOM_PORT 를 사용했다.   
운영 중인 API 서버는 맵서버와 통신을 위해 사용하는 Bean 객체가 있다. 그렇기 때문에 SpringBoot Test 에서 제공하는 webEnvironment 값을 MOCK(디폴트 값)이 아닌 RANDOM_PORT 를 사용했다.



### 2. @Transactional 어노테이션 미사용

테스트 메서드, 테스트 클래스 모두에 @Transactional 어노테이션을 사용하고 있지 않았다. @Transactional 어노테이션을 사용하면 단순히 해결될 줄 알았다.


## 소스코드 수정

### 1. @SpringBootTest(webEnvironment=WebEnvironment.MOCK)

webEnvironment=WebEnvironment.RANDOM_PORT 의 경우 테스트가 진행되는 스레드와 DB 커넥션을 갖는 스레드는 별도의 스레드기 때문에, 롤백이 불가능하다.   
그래서 webEnvironment=WebEnvironment.MOCK 으로 수정했고, 맵서버와 통신을 위해 사용하는 Bean 객체만 Mock 객체(@MockBean)로 만들었다.

### 2. @Transactional 어노테이션 추가

테스트 메서드, 테스트 클래스에 @Transactional 어노테이션을 추가했다.(서비스 레이어에는 이미 @Transactional 어노테이션이 추가돼있었다.)

## 응?

여전히 정상적으로 롤백이 되지 않았다.
테스트 케이스 실행 후 로그를 확인하면 정상적으로 롤백되었다고 나온다.

```
[           main] Began transaction (1) for test context [DefaultTestContext@7188af83 ~~ rollback [true]
[           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
[           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
Query문 (UPDATE)
[           main] o.s.t.c.transaction.TransactionContext   : Rolled back transaction for test: ~
```

## 원인

DB 커넥션을 생성하기 위해 DataStore 객체를 만드는데, 운영중인 API 는 HikariDataSource 를 사용중이었다.
그리고 HikariDataSource.java 는 HikariConfig.java 를 상속받는데

```java
public class HikariDataSource extends HikariConfig implements DataSource, Closeable
{
    //중략
}
```

HikariConfig 클래스의 디폴트 설정값을 보니 AutoCommit 이 True 로 되어있었다.

```java
public class HikariConfig implements HikariConfigMXBean {
    
    // 중략
    
    /**
     * Default constructor
     */
    public HikariConfig()
    {
        dataSourceProperties = new Properties();
        healthCheckProperties = new Properties();

        minIdle = -1;
        maxPoolSize = -1;
        maxLifetime = MAX_LIFETIME;
        connectionTimeout = CONNECTION_TIMEOUT;
        validationTimeout = VALIDATION_TIMEOUT;
        idleTimeout = IDLE_TIMEOUT;
        initializationFailTimeout = 1;
        isAutoCommit = true;

        String systemProp = System.getProperty("hikaricp.configurationFile");
        if (systemProp != null) {
            loadProperties(systemProp);
        }
    }
    
    //중략
    
}
```

## 해결

```java
class DataBaseConfig {
    
    //중략
    
    private DataSource dataSource(String crtfckey, String driverClassName, String jdbcUrl, String username, String password) {
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setJdbcUrl(jdbcUrl);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        dataSource.setAutoCommit(false);
        return dataSource;
    }
    
    //중략
}

```