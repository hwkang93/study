# java.lang.reflect.UndeclaredThrowableException

```java
package java.lang.reflect;

public class UndeclaredThrowableException extends RuntimeException {
    static final long serialVersionUID = 330127114055056639L;
    private Throwable undeclaredThrowable;

    public UndeclaredThrowableException(Throwable undeclaredThrowable) {
        super((Throwable)null);
        this.undeclaredThrowable = undeclaredThrowable;
    }

    public UndeclaredThrowableException(Throwable undeclaredThrowable, String s) {
        super(s, (Throwable)null);
        this.undeclaredThrowable = undeclaredThrowable;
    }

    public Throwable getUndeclaredThrowable() {
        return this.undeclaredThrowable;
    }

    public Throwable getCause() {
        return this.undeclaredThrowable;
    }
}
```

호출 핸들러의 invoke 메소드가 메소드의 throws 절에 선언된 예외에 할당할 수 없는 예외를 throw 하는 경우,
UndeclaredThrowableException 예외에 래핑되어 throw 된다.

**getUndeclaredThrowable()** 메소드를 통해 원래의 예외를 확인할 수 있다.