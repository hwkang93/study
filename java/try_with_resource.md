# Try-With-Resource

자바에서 외부 자원에 접근하는 경우 접근이 끝난 후 반드시 자원을 반납해야 한다.
DB Connection 객체 생성을 예로 들면 다음과 같은 소스코드를 작성할 수 있다.

```java
public class DbConnectionExample {
    public static void main(String[] args) {
        final String url = "jdbc:postgresql://localhost:5432/database";
        final String user = "postgresql";
        final String password = "postgresql";

        Connection connection = null;
        try {
            connection = DriverManager.getConnection(url, user, password);
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                if(connection != null) {
                    connection.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## Try-With-Resource 란?

불필요한 소스코드 및 개발자의 번거로움을 줄이고, 자원의 반납을 보장해주기 위해 만들어진 구문이다.
Java 1.7 부터 사용할 수 있으며, 기존의 try-catch 문과 거의 동일하나


## Try-With-Resource 사용 조건

```try ()``` 안에 선언될 객체는 반드시 ```AutoClosable``` 인터페이스의 구현체여야 한다.