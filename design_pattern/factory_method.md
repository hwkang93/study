# Factory Method

슈퍼 클래스에 알려지지 않은 구체 클래스를 생성하는 패턴이며, 서브 클래스가 어떤 객체를 생성할지를 결정하도록 하는 패턴이기도 하다.
슈퍼 클래스 코드에 구체 클래스 이름을 감추기 위한 방법으로도 사용된다.

팩토리 메소드 패턴은 
- 클라이언트가 클라이언트에서 생성해야 하는 객체의 구현체를 예측할 수 없을 때나,
- 생성한 객체를 기술하는 책임을 다른 클래스가 가졌으면 할 때

사용된다.


## 장단점

### 장점

- 인스턴스의 타입을 결정하는 클래스가 따로 존재하여 객체들을 한 곳에서 관리할 수 있다는 장점이 있음
- 인터페이스가 정해져 있어 확장성이 용이함
- 구현체가 아닌 인터페이스에 의존하는 DIP 를 적용한 설계가 가능하다.

### 단점

- 새로운 타입의 인스턴스를 만들 때마다 구현체가 증가하여 복잡도가 증가할 가능성 존재 

## 예시

> [전체 소스코드 보러 가기](https://github.com/hwkang93/study-sample-source/tree/main/design-pattern/src/factory_method)

팩토리 메소드 패턴을 사용해 할인 쿠폰을 생성하는 방법을 예시로 들어보겠다.
예시에서 사용할 할인 쿠폰은 배달 할인 쿠폰과 주문 할인 쿠폰 두 가지가 있고,
각각 쿠폰 타입, 쿠폰 명, 금액, 만료일자의 인자를 가지고 있다.

팩토리 메소드 패턴을 사용하기 위해서는 슈퍼 클래스가 필요하다.
슈퍼 클래스는 추상클래스, 인터페이스, 클래스 등 타입이 상관없지만, 예제에서는 추상클래스를 사용했다.

먼저 슈퍼클래스 ```DiscountCoupon``` 과 서브클래스 ```OrderDiscountCoupon``` , ```DeliveryDiscountCoupon``` 를 생성한다.

```DiscountCoupon.java```

```java
import java.time.LocalDate;

public abstract class DiscountCoupon {

    abstract public DiscountCouponType getType();
    abstract public String getCouponName();
    abstract public short getAmount();
    abstract public LocalDate getExpiryDate();

    public boolean isAvailable() {
        return !this.getExpiryDate().isBefore(LocalDate.now());
    }

    @Override
    public String toString() {

        return this.getType() + " Coupon :: Coupon Name : " + this.getCouponName() +
                " , Amount : " + this.getAmount() +
                ", ExpiryDate : " + this.getExpiryDate();
    }
}
```

```DeliveryDiscountCoupon```

```java
import java.time.LocalDate;

public class DeliveryDiscountCoupon extends DiscountCoupon {

    private DiscountCouponType discountCouponType;
    private String couponName;
    private short amount;
    private LocalDate expiryDate;

    public DeliveryDiscountCoupon(DiscountCouponType discountCouponType, String couponName, short amount, LocalDate expiryDate) {
        this.discountCouponType = discountCouponType;
        this.couponName = couponName;
        this.amount = amount;
        this.expiryDate = expiryDate;
    }

    @Override
    public DiscountCouponType getType() {
        return this.discountCouponType;
    }

    @Override
    public String getCouponName() {
        return this.couponName;
    }

    @Override
    public short getAmount() {
        return this.amount;
    }

    @Override
    public LocalDate getExpiryDate() {
        return this.expiryDate;
    }
}
```

```OrderDiscountCoupon.java```

```java
public class OrderDiscountCoupon extends DiscountCoupon {

    private DiscountCouponType discountCouponType;
    private String couponName;
    private short amount;
    private LocalDate expiryDate;

    public OrderDiscountCoupon(DiscountCouponType discountCouponType, String couponName, short amount, LocalDate expiryDate) {
        this.discountCouponType = discountCouponType;
        this.couponName = couponName;
        this.amount = amount;
        this.expiryDate = expiryDate;
    }

    @Override
    public DiscountCouponType getType() {
        return this.discountCouponType;
    }

    @Override
    public String getCouponName() {
        return this.couponName;
    }

    @Override
    public short getAmount() {
        return this.amount;
    }

    @Override
    public LocalDate getExpiryDate() {
        return this.expiryDate;
    }
}
```

할인 쿠폰의 타입을 결정하는 ```enum``` 객체 ```DiscountCouponType``` 도 만들어 준다.
이 ```DiscountCouponType``` 에서 선언한 쿠폰 타입에 따라 구체 클래스가 결정되도록 구현할 것이다.

```java
public enum DiscountCouponType {
    DELIVERY, ORDER;
}
```

이제 팩토리 클래스를 만들어보겠다. 팩토리 클래스에서 객체를 생성하는 역할을 하는 메소드는 ```static``` 으로 만들어주며,
팩토리 클래스의 인스턴스화를 막기 위해 ```private``` 생성자도 추가해주겠다.

```DiscountCouponFactory.java```

```java
import java.time.LocalDate;

public class DiscountCouponFactory {

    private DiscountCouponFactory(){}
    
    public static DiscountCoupon create(DiscountCouponType discountCouponType, String couponName, short amount, LocalDate expiryDate) {

        return switch (discountCouponType) {
            case DELIVERY   -> new DeliveryDiscountCoupon(discountCouponType, couponName, amount, expiryDate);
            case ORDER      -> new OrderDiscountCoupon(discountCouponType, couponName, amount, expiryDate);
            default         -> null;
        };
    }
}
```

위에 선언한 ```create()``` 는 파라미터로 ```DiscountCouponType``` 를 받아
분기 처리를 통해 타입에 맞는 서브 클래스를 인스턴스화 하여 리턴한다.

다음은 할인 쿠폰을 생성하는 클라이언트 코드이다.

```FactoryMethodPatternMain.java```

```java
import java.time.LocalDate;

public class FactoryMethodPatternMain {
    public static void main(String[] args) {
        DiscountCouponType discountCouponType = DiscountCouponType.DELIVERY;
        String couponName = "1주년 기념 배달비 1000원 할인 쿠폰";
        short amount = 1000;
        LocalDate expiryDate = LocalDate.of(2021, 03, 20);

        DiscountCoupon coupon = DiscountCouponFactory.create(discountCouponType, couponName, amount, expiryDate);

        System.out.println(coupon.toString());
        System.out.println(coupon.isAvailable());
    }
}
```

```
DELIVERY Coupon :: Coupon Name : 1주년 기념 배달비 1000원 할인 쿠폰 , Amount : 1000, ExpiryDate : 2021-03-20
```

이렇게 팩토리 메소드 패턴을 사용하면 클라이언트는 구현체를 직접 선택하지 않고 인터페이스(혹은 추상클래스 등 슈퍼클래스)에 의존하는 형태의 소스코드로 구현이 가능하고,
인스턴스의 타입 결정하는 역할을 가진 하나의 클래스만 관리할 수 있다는 장점을 가질 수 있다.
만약 새로운 구현체가 추가된다고 하더라도, 인터페이스가 정해져있기 때문에 확장성에서도 유리함을 가져갈 수 있다.

## 사용 예시

- java.util 패키지에 있는 Calendar, ResourceBundle, NumberFormat 등의 클래스에서 정의된 getInstance() 메소드
- Boolean, Integer, Long 등 Wrapper class 안에 정의된 valueOf() 메소드

## Effective Java 에서의 Static Factory Method

Effective Java 가장 첫 번째 규칙이 Static Factory Method 를 고려하라는 것이다.
위에서 설명한 Factory Method 패턴은 Effective JAVA 에서 나오는 Static Factory Method 와 **목적**이 다르다.
둘 다 생성자를 대신 만들어주는 의미의 메소드로 Factory 를 사용하지만,
**Factory Method 패턴은 팩토리 메소드가 구현체 클래스의 결정을 하는 것을 목적으로 하고 있고**,
Effective JAVA 에서의 Static Factory Method 는 new 키워드로 객체를 생성하는 것이 아닌, static 메소드를 활용해 객체를 생성하는 것을 목적으로 하고 있다.
(Effective JAVA 책을 다시 읽어보니 Factory Method 패턴과는 다른 개념이라고 설명 돼있었다.)

> **Effective JAVA 에서 말하는 Static Factory Method**
> 
> new 키워드로 인스턴스를 생성하는 것이 아닌 정적 메소드를 사용해 인스턴스를 생성하는 방식.
> 
> Static Factory Method 패턴의 장점으로는
> 
> new 키워드로 만드는 생성자와는 달리 이름을 가질 수 있고,
> 
> 호출할 때마다 새로운 객체를 생성하지 않아도 되고,
> 
> 반환값 자료형의 하위 자료형 객체를 반환할 수 있고,
> 
> ~~형인자 자료형(parameterized type) 객체를 만들 때 편하다.~~
> 

## Reference

[팩토리 메서드 패턴 - 위키백과](https://ko.wikipedia.org/wiki/%ED%8C%A9%ED%86%A0%EB%A6%AC_%EB%A9%94%EC%84%9C%EB%93%9C_%ED%8C%A8%ED%84%B4)

[[생성 패턴] 팩토리 패턴(Factory Pattern) 이해 및 예제 - 준비된 개발자](https://readystory.tistory.com/117)

Effective JAVA - 조슈아 블로크