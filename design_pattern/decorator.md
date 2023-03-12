# 데코레이터 패턴

데코레이터 패턴은 객체 지향 프로그래밍에서 사용되는 디자인 패턴 중 하나로 기존 객체에 동적으로 새로운 기능을 추가하거나 기존 기능을 수정할 수 있게 하는 패턴이다. 
데코레이터 패턴은 상속을 사용하지 않고 객체의 기능을 확장하기 위한 유연한 방법을 제공한다.

데코레이터 패턴의 핵심 아이디어는 기능의 계층 구조를 만들어 객체를 감싸는 것이다. 
각 데코레이터는 같은 인터페이스를 구현하는 기능 객체를 감싸며, 데코레이터는 자체적인 동작을 수행한 후 내부에 감싼 기능 객체의 동작을 호출한다. 
이런 식으로 데코레이터를 계층적으로 쌓을 수 있으며, 객체에 여러 개의 데코레이터를 적용할 수 있다. 이를 통해 객체의 동작을 동적으로 조합하고 변경할 수 있다.

## 구성 요소

### Component

기능의 인터페이스를 정의하는 역할을 한다. 이 인터페이스는 데코레이터와 실제 객체 모두가 구현해야 한다.

### ConcreteComponent

기능의 실제 구현을 담당한다. 이 클래스는 기본 동작을 정의한다.

### Decorator

Component 인터페이스를 구현하고, 내부에 Component 객체를 감싸는 역할을 한다. 데코레이터는 동일한 인터페이스를 가지고 있으므로 다른 데코레이터나 실제 객체를 감쌀 수 있다.

### ConcreteDecorator

Decorator 클래스를 상속받아 구현하는 클래스이다. 이 클래스는 새로운 기능을 추가하거나 기존 기능을 수정하는 역할을 한다.


데코레이터 패턴을 사용하면 객체의 기능을 동적으로 확장하고 조합할 수 있다. 
예를 들어, 웹 애플리케이션에서 사용자에게 특정 권한을 부여하는 기능을 구현한다고 가정해 보자. 데코레이터 패턴을 사용하면 사용자 객체에 권한을 부여하는 데코레이터를 적용할 수 있다.

또 다른 예로는 파일 입출력 기능을 가진 객체에 암호화 기능을 추가하는 경우를 들 수 있다. 이 경우, 파일 입출력 객체를 감싸는 암호화 데코레이터를 적용하여 원본 데이터를 암호화할 수 있다.

데코레이터 패턴은 상속을 사용하지 않고도 객체의 기능을 확장할 수 있는 장점을 제공한다. 
또한, 데코레이터는 독립적으로 추가하거나 제거할 수 있으므로 객체의 동작을 유연하게 변경할 수 있다. 그러나 데코레이터 패턴을 오용하면 코드가 복잡해질 수 있으므로 적절하게 사용해야 한다.

## 예제 소스코드

다음은 예제 소스코드이다. 데코레이터 패턴의 가장 대표적인 커피 주문 시스템을 구현하고, 커피에 추가적인 옵션을 적용하는 예제이다.

1. 먼저 커피 주문 시스템의 기본 컴포넌트를 정의한다.

```java
// 커피 주문 시스템 인터페이스
public interface Coffee {
    double getCost();
    String getDescription();
}
```

2. 기본 커피를 나타내는 클래스로, ```Coffee``` 인터페이스를 구현한다.

```java
// 기본 커피
public class SimpleCoffee implements Coffee {
    @Override
    public double getCost() {
        return 2.0;
    }

    @Override
    public String getDescription() {
        return "Simple Coffee";
    }
}
```

3. 데코레이터의 기반이 되는 추상 클래스로, ```Coffee``` 인터페이스를 구현하고 내부에 ```Coffee``` 객체를 감싸는 역할을 한다.

```java
// 커피 데코레이터
public abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;

    public CoffeeDecorator(Coffee coffee) {
        this.decoratedCoffee = coffee;
    }

    @Override
    public double getCost() {
        return decoratedCoffee.getCost();
    }

    @Override
    public String getDescription() {
        return decoratedCoffee.getDescription();
    }
}
```

4. 실제 데코레이터들로, 각각 우유와 설탕을 추가하는 역할을 한다.

```java
// 우유 데코레이터
public class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public double getCost() {
        return super.getCost() + 0.5;
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", Milk";
    }
}

// 설탕 데코레이터
public class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public double getCost() {
        return super.getCost() + 0.2;
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", Sugar";
    }
}
```

5. 데코레이터 패턴을 사용하여 커피에 추가적인 옵션을 적용하는 방법이다.

```java
public class Main {
    public static void main(String[] args) {
        // 기본 커피 주문
        Coffee coffee = new SimpleCoffee();
        System.out.println("Cost: " + coffee.getCost() + ", Description: " + coffee.getDescription());

        // 우유를 추가한 커피 주문
        Coffee coffeeWithMilk = new MilkDecorator(new SimpleCoffee());
        System.out.println("Cost: " + coffeeWithMilk.getCost() + ", Description: " + coffeeWithMilk.getDescription());

        // 설탕을 추가한 커피 주문
        Coffee coffeeWithSugar = new SugarDecorator(new SimpleCoffee());
        System.out.println("Cost: " + coffeeWithSugar.getCost() + ", Description: " + coffeeWithSugar.getDescription());

        // 우유와 설탕을 추가한 커피 주문
        Coffee coffeeWithMilkAndSugar = new SugarDecorator(new MilkDecorator(new SimpleCoffee()));
        System.out.println("Cost: " + coffeeWithMilkAndSugar.getCost() + ", Description: " + coffeeWithMilkAndSugar.getDescription());
    }
}
```

위의 예제에서는 커피 주문 시스템을 구현하고, 데코레이터 패턴을 사용하여 커피에 우유와 설탕을 추가하는 기능을 구현했다.
데코레이터 패턴을 사용하면 컴포넌트에 동적으로 기능을 추가할 수 있으며, 기존 코드 변경 없이도 새로운 기능을 쉽게 조합할 수 있다.