# Assertions Class 패키지

Assertions 클래스 ```static import``` 하는데 맨날 까먹어서 메모함.

```
import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;
```

## org.assertj.core.api.Assertions

### assertThat

```
String userName = "Tester1";
long userId = 1L;

UserResponse userResponse = userService.findByUserId(long userId);

Assertions.assertThat(userResponse.getUserName()).isEqualTo(userName);
```

### doesNotThrowAnyException

```
long userId = 1L;

Assertions.assertThatCode(() -> userService.findByUserId(long userId)).doesNotThrowAnyException();
```

## org.junit.jupiter.api.Assertions

### assertThrow

```
long userId = 1L;

Assertions.assertThrows(NullPointerException.class, () -> userService.findByUserId(userId));
```

### assertNotNull

### assertTrue / assertFalse

```
long userId = 1L;

UserResponse userResponse = userService.findByUserId(long userId);

Assertions.assertTrue(userResponse.isAdmin());
```