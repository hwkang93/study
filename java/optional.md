# Optional

JAVA 8 에 추가된 null 값을 가지고 있을 수 있는 컨테이너 인스턴스 타입


- null 을 가질 수 있는 모든 객체에 대해 null 체크를 해야 한다. 
 -> 에러를 만들기 좋은 코드

- null 을 리턴하는 것 자체도 문제

- 에러를 발생시켜야 하는데 에러가 발생하면 자바에서는 stack trace 을 출력하고
이 자체에 리소스가 소모된다. 

> 로직을 처리하는 데 에러를 사용하는 것은 좋은 습관이 아니다.
> 쓸데없는 리소스를 소모하기 때문에

null 값이 전달될 수 있는 경우에 Optional 객체로 감싸서 리턴하면
클라이언트 입장에서 해당 객체가 null 일 수 있음을 인지할 수 있다.


## Optional 는 언제 사용하는 게 좋을까?

Optional 은 모든 위치에서 사용할 수 있지만, **리턴 타입에 사용하는 걸 권장한다.**

매개변수 타입으로 Optional<T> 를 사용하는 것은 권장되지 않는다.

그 이유는 Optional 객체 자체가 null 이 될 수 있고 문법적으로는 아무 문제가 없다. 
그렇기 때문에 코드상으로 객체에서 null 여부를 확인해야 하는데, 그럼 Optional 를 사용하는 의미가 없다.

**Map 객체에서 Key 값을 Optional 을 사용하지 않는다.**

Map 이 가지고 있는 가장 큰 특징이 **Map 객체에서 Key 값은 null 이 될 수 없다.**

**객체의 필드 값으로 사용하지 않는다.**

도메인 클래스 설계 단계에서 해결해야 할 문제이다.

**Optional 이 리턴 타입인 경우, 어떤 상황에서도 null 을 리턴하지 않는다.**

클라이언트에서 null 을 또 확인해야 한다. Optional 을 사용할 이유가 없다.
반환할 객체가 없는 경우 ```Optional.empty()``` 를 리턴한다.

Map, Stream, Collection, Array 등 다른 컨테이너 인스턴스는 비어있을 수 있는 객체이다.
Optional 로 다시 감싸면 박싱, 언박싱이 두 번 이루어져서 의미가 없다.
그렇기 때문에 Optional 로 감싸지 말 것.


primitive 타입의 Optional

```Optional.of(10)```

primitive 타입을 Optional 로 사용하는 것은 권장하지 않는다.

박싱, 언박싱을 두번씩 해야 하기 때문에 


## Optional 사용 예제

들어가기 앞서 테스트를 위해 "학생" 클래스를 만들어주겠다. 
학생 클래스는 이름, 나이, 동아리 필드를 가지고 있다.

```java
public class Student {
    private String name;
    private int age;
    private Club club;

    public Student(String name, int age, Club club) {
        this.name = name;
        this.age = age;
        this.club = club;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public Club getClub() {
        return club;
    }
}
```

동아리 클래스도 만들어주겠다. 동아리 클래스에는 동아리 명을 필드로 가진다.

```java
public class Club {
    private String name;

    public Club(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

테스트를 위한 메인 클래스를 만들고 테스트데이터를 메모리에 올린다.

```java
public class OptionalMain {
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
                new Student("khw", 30, new Club("퍼즐 동아리")),
                new Student("chr", 30, new Club("방탈출 동아리"))
        );
    }
}
```

이제 Optional 에서 제공하는 API 들을 살펴보겠다. 

### orElse

먼저 orElse 메소드이다. orElse 는 optional 객체가 비어있을 경우(null) orElse 메소드 내에 있는 객체가 리턴된다.

- **예제**
```java
public class OptionalMain {
    public static void main(String[] args) {
        // 생략

        Optional<Student> startWithK = students.stream()
                .filter(s -> s.getName().startWith("k"))
                .findFirst();
        
        Student student = startWithK.orElse(createStudent());

        System.out.println(student.getName());
    }
    
    public static Student createStudent() {
        System.out.println("Create Student.");
        return new Student("default", 0, null);
    }
}
```

- **결과**

```
Create Student.
khw
```

optional 객체가 비어있지 않더라도 orElse 메소드는 실행된다.

- **예제**
```java
public class OptionalMain {
    public static void main(String[] args) {
        // 생략

        Optional<Student> startWithH = students.stream()
                .filter(s -> s.getName().startWith("h"))
                .findFirst();
        
        Student student = startWithH.orElse(createStudent());

        System.out.println(student.getName());
    }
    
    public static Student createStudent() {
        System.out.println("Create Student.");
        return new Student("default", 0, null);
    }
}
```

- **결과**

```
Create Student.
default
```

### orElseGet

orElse 와 달리 Optional 객체가 비어있는 경우에만 실행되는 메소드이다. 
orElseGet 은 파라미터로 Supplier 를 받는다.

- **예제**

```java
public class OptionalMain {
    public static void main(String[] args) {
        // 생략

        Optional<Student> startWithK = students.stream()
                .filter(s -> s.getName().startWith("k"))
                .findFirst();
        
        Student student1 = startWithK.orElseGet(OptionalMain::createStudent);
        System.out.println(student1.getName());

        Optional<Student> startWithH = students.stream()
                .filter(s -> s.getName().startWith("h"))
                .findFirst();

        Student student2 = startWithH.orElseGet(OptionalMain::createStudent);
        System.out.println(student2.getName());
    }
    
    public static Student createStudent() {
        System.out.println("Create Student.");
        return new Student("default", 0, null);
    }
}
```

- **결과**

```
khw
Create Student.
default
```

상수로 디폴트 값이 정의되어 있는 경우에는 orElse 메소드를 사용하는 것이 더 적합하고,
동적으로 판단해야 하는 경우에는 orElseGet 메소드를 사용하는 것이 더 적합하다.

### orElseThrow

객체가 비어있을 경우 예외를 리턴한다.
기본값으로 ```RuntimeException``` 을 리턴하며, 파라미터로 다른 예외를 리턴할 수 있다.
사용자 정의 예외도 리턴이 가능하다.

### filter

Optional 객체가 있다고 가정하고 필터링을 하는 메소드이다.
Optional 객체가 리턴되며, 결과 값이 없을 경우에는 Empty Optional 객체가 리턴된다.

## Reference

[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8/dashboard)



