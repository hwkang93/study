# DB Connection Test Example

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