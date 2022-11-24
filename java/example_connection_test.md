# DB Connection Test Example

데이터베이스 커넥션 연결을 테스트하는 예제 소스코드이다.

```java
import java.sql.Connection;
import java.sql.DriverManager;

class ConnectionUtils {
    //...
    public static boolean connectionTest(String url, String user, String password) {
        log.info("========== DB Connection Test ==========");
        log.info("  Connection info ==> url : {} user : {} password : {}", url, user, password);

        try (Connection connection = DriverManager.getConnection(url, user, password);) {
            if(connection != null) {
                log.info("  Connection test result : Success");
                return true;
            }
        } catch (SQLException se) {
            log.error("  Database connection Error : ", se);
        } catch (Exception e) {
            log.error("  Exception occur : " + e);
        }

        log.info("  Connection test result : Fail");

        return false;
    }
    //...
}
```

### 참고

PostgreSQL 을 사용하는 경우 URL 을 다음과 같이 입력한다.

> jdbc:postgresql://localhost:5432/database_name

PostgreSQL 9.4 버전 이상을 사용하고 있다면 쿼리스트링으로 스키마를 받을 수 있는데, 다음과 같이 사용하면 된다.

> jdbc:postgresql://localhost:5432/database_name?currentSchema=schema_name