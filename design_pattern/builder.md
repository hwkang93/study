# Builder Pattern

Builder Pattern 은 객체의 인스턴스를 생성자를 통해 직접 생성하지 않고, Builder 라는 내부 클래스를 통해 간접적으로 생성하게 하는 패턴이다.

```java
public class Member {
    //이름과 나이는 필수
    private final String name;
    private final short age;
    
    private String address;
    private String addressDetail;
    
    //생성자는 private 으로 선언
    private Member(String name, short age, String address, String addressDetail) {
        this.name = name;
        this.age = age;
        this.address = address;
        this.addressDetail = addressDetail;
    }
    
    //static class 로 선언
    public static class Builder {
        final String name;
        final short age;
        
        //선택값의 경우 적절한 값으로 초기화
        String address = null;
        String addressDetail = null;
        
        //필수 인자가 있는 경우 Builder 생성자의 파라미터로 받음
        public Builder(String name, short age) {
            this.name = name;
            this.age = age;
        }
        
        public Builder address(String address) {
            this.address = address;
            
            return this;
        }
        
        public Builder addressDetail(String addressDetail) {
            this.addressDetail = addressDetail;
            
            return this;
        }
        
        public Member build() {
            return new Member(name, age, address, addressDetail);
        }
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        String name = "kang";
        short age = 10;
        
        Member member = new Member.builder(name, age)
                .address("서울특별시")
                .build();               //addressDetail 의 경우 선택인자이므로 입력하지 않아도 상관 없음.
    }
}
```

## 사용 목적

- **클래스와 사용 대상의 결합도를 낮추기 위해**

예를 들어 설명하겠다.

```java
class Member {
    private final String name;
    private final short age;
    private String address;
    
    public Member(String name, short age, String address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }
    
    //... 이하 생략
}
```

```Member``` 클래스가 있고, 프로젝트 내 대부분의 서비스들이 ```Member``` 클래스를 사용하고 있다. 
그런데, 데이터 변경 등 어떠한 변경으로 인해 ```Member``` 클래스의 생성자에 인자로 Job 을 추가해야 되는 상황이라면 어떻게 해야 할까?

```java
class Member {
    private final String name;
    private final short age;
    private String address;
    
    //신규 추가
    private final Job job;
    
    public Member(String name, short age, String address, Job job) {
        this.name = name;
        this.age = age;
        this.address = address;
        this.job = job;
    }
    
    //... 이하 생략
}
```

```Member``` 클래스에는 Job 이라는 컬럼이 추가될 것이고, 생성자에도 인자를 하나 추가해야 할 것이다.
그렇게 되면 ```Member``` 클래스로 생성자 객체를 만든 모든 곳에서 컴파일 오류가 날 것이고, 해당 클래스를 생성하는 모든 부분의 코드를 일일히 다 수정해야 할 것이다.

신규 개발 중인 프로젝트는 어찌어찌 Ctrl + F 로 코드 찾아가면서 해당 클래스의 생성자를 전부 변경을 하겠지만, 이 부분이 다른 사람에게 배포하여 사용하는 Library 같은 것이라면 어떻게 될까?

Builder 는 해당 문제점을 해결하기 위해 만들어진 패턴이다. 
대상 클래스의 생성자는 private 등의 접근 제한자로 제한하여 외부에서 임의로 접근하는 것을 막아 클래스와 사용 대상의 결합도를 떨어트리고, 대신 Builder 라는 내부 클래스를 통해 해당 클래스를 간접적으로 생성한다.

Builder 에서 필수값이 아닌 선택값은 디폴트 값으로 초기화를 하여 해당 인수에 값을 할당하지 않더라도 컴파일 자체는 가능하며, 사용자에 요청에 따라 값을 할당할 수도 있다.

- **생성자에 전달하는 인수에 의미를 부여하기 위해**

생성자에 필요한 파라미터 값이 여러 개인 클래스의 경우 생성자 패턴으로 인수를 전달하는 것이 상당히 비직관적이다.
생성자에 필요한 모든 파라미터의 종류를 외워서 써야 하고, 인수의 순서도 고려해야 하며, 파라미터가 같은 타입인 경우 인수 전달에 실수를 하더라도 컴파일에서 오류를 잡아주지 않는다.

## Lombok 라이브러리를 통한 Builder 패턴 구현

Builder 패턴에도 단점이 존재한다. 

위에서 알아보았던 Builder 패턴 예제를 보면, 파라미터의 양이 많던 적던 간에 Builder 패턴을 구현해야 하는 코드가 존재하고, 코드의 양도 만만치 않다.
Builder 패턴으로 작성해야 하는 클래스의 양이 많아질수록 코딩을 해야 하는 개발자들은 쓸데없는 곳에 시간을 소모해야 한다. 
이럴 때 Lombok Library 를 사용하면 엄청난 시간을 단축할 수 있다.
이번 정리에서는 Lombok 라이브러리를 통해 Builder 패턴을 구현하는 예제만 간단하게 기록하고 마치도록 하겠다.

```java
public class Member {
    
    private final String name;
    private final short age;
    
    private String address;
    private String addressDetail;
    
    @Builder
    private Member(String name, short age, String address, String addressDetail) {
        this.name = name;
        this.age = age;
        this.address = address;
        this.addressDetail = addressDetail;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        String name = "kang";
        short age = 10;
        
        Member member = new Member.builder()
                .name(name)
                .age(age)
                .address("서울특별시")
                .build();               //addressDetail 의 경우 선택인자이므로 입력하지 않아도 상관 없음.
    }
}
```

## Reference

[나무위키 - 디자인 패턴](https://namu.wiki/w/%EB%94%94%EC%9E%90%EC%9D%B8%20%ED%8C%A8%ED%84%B4)