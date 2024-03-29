# Soft Delete at JPA

데이터베이스의 데이터를 삭제하는 방법에는 **Hard Delete**와 **Soft Delete** 두 종류가 있다.

- Hard Delete : delete 쿼리를 날려 데이터를 실제로 삭제하는 방법
- Soft Delete : 데이터베이스를 실제로 삭제하는 것이 아니라, deleted 와 같은 삭제 여부 필드를 추가하고, 삭제 여부의 값을 통해 데이터의 상태를 결정하는 것

Soft Delete 의 경우 조회 쿼리 결과로 삭제된 값을 조회하면 안되기 때문에 조건절(where)에 삭제 여부를 추가해 삭제되지 않은 데이터만 조회하거나,
어플리케이션 레벨에서 필터링을 통해 사용자에게 삭제되지 않은 데이터만 보여줘야 한다.

하지만, 모든 데이터 조회마다 직접 조건을 달아준다면 빼먹을 가능성이 있다. 그렇기 때문에 JPA 에서는 
- 삭제 시 delete 쿼리 대신 사용자가 직접 입력한 쿼리를 실행시켜 주는 기능과
- 엔티티 조회 시 모든 쿼리에 where 조건을 추가해주는 기능을 제공하고 있다. 

## 예시

다음 Shop Entity 를 예로 들어보자. 처음 생성 시 삭제 여부 기본값을 false 로 지정한다.

```java
@Entity
public class Shop {

	@Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    private String address;
    
    private boolean deleted = Boolean.FALSE; // 삭제 여부 기본값 false
}
```

다음은 Entity 를 삭제하는 테스트 코드이다. delete 호출 이후에 삭제된 Shop 을 조회하려고 하면, 실제 데이터베이스의 값이 없기 때문에 afterDelete 값이
비어있게 된다.

```java
@Test
@DisplayName("Shop soft delete")
public void delete() {
    Shop shop = new Shop("나이키", "서울 어딘가");
    Shop savedShop = shopRepository.save(shop);

    assertThat(savedShop.getId()).isNotNull();
    assertThat(savedShop.isDeleted()).isFalse();

    shopRepository.delete(savedShop);
    entityManager.flush();

    Optional<Shop> afterDelete = shopRepository.findById(savedShop.getId());
    assertThat(afterDelete).isEmpty();
}
```

delete 쿼리를 로그로 확인할 수도 있다

```
    delete 
    from
        shop 
    where
        id=?
```

## @SQLDelete

Soft Delete 를 하기 위해 delete 메소드 실행 시 delete 쿼리 대신, update 쿼리를 통해 삭제 여부 필드(deleted) 만 true 로 변경시켜줄 수 있다.
이 때 사용하는 어노테이션이 ```@SQLDelete``` 이다.

```@SQLDelete``` 는 엔티티 삭제가 발생했을 때, delete 쿼리 대신 실행시켜 줄 커스텀 sql 구문을 말한다. ```@SQLDelete``` 어노테이션이 있는 Entity 는
삭제 요청 시 delete 쿼리 대신 커스텀 sql 구문이 실행된다.

```
@Entity
@SQLDelete(sql = "UPDATE shop SET deleted = true WHERE id = ?")
public class Shop {

	@Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    private String address;
    
    private boolean deleted = Boolean.FALSE; // 삭제 여부 기본값 false
}
```

아까 했던 테스트 코드를 조금 수정해 다시 실행시켜보자. 실제로 데이터가 삭제된 것이 아니기 때문에 optional 객체가 비어있지 않고, deleted 값이 true 인 것을 
확인할 수 있다.

```java
@Test
@DisplayName("Shop soft delete")
public void delete() {
        Shop shop = new Shop("나이키", "서울 어딘가");
        Shop savedShop = shopRepository.save(shop);

        assertThat(savedShop.getId()).isNotNull();
        assertThat(savedShop.isDeleted()).isFalse();

        shopRepository.delete(savedShop);
        entityManager.flush();

        Optional<Shop> afterDelete = shopRepository.findById(savedShop.getId());
        assertThat(afterDelete).isNotEmpty();
        assertThat(afterDelete.get().isDeleted()).isTrue();
        }
```

update 쿼리가 발생한 것도 볼 수 있다.

```
    UPDATE
        shop 
    SET
        deleted = true 
    WHERE
        id = ?
```

> SQLDelete는 영속성컨텍스트에서 관리되다가 트랜잭션이 끝나고 실제 DB에 쿼리를 보낼때 처리를 해준다고 한다.


## @Where

Soft Delete 처리된 데이터를 조회하는 경우, 삭제 여부 필드가 false 인 데이터만 조회해야 한다. Entity 를 사용하는 모든 데이터에서 삭제되지 않은 데이터만 조회해야 한다면
```@Where``` 를 이용할 수 있다.

```@Where``` 어노테이션은 Entity 에 기본적으로 적용할 조건을 뜻하는 어노테이션이다. 일반적으로 Soft Delete 에서 사용된다.

아래와 같이 Entity 에 ```@Where``` 어노테이션을 추가하면 Entity 조회 시 디폴트 조건이 추가된다.

```java
@Entity
@SQLDelete(sql = "UPDATE shop SET deleted = true WHERE id = ?")
@Where(clause = "deleted = false")
public class Shop {

	@Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    private String address;
    
    private boolean deleted = Boolean.FALSE; // 삭제 여부 기본값 false
}
```

다음은 Shop Entity 를 findAll 함수를 통해 목록을 호출하는 쿼리이다. findAll 메소드만 호출하더라도 deleted = false 조건이 추가된 걸 확인할 수 있다.

```sql
    select
        shop0_.id as id1_11_,
        shop0_.address as address2_11_,
        shop0_.deleted as deleted3_11_,
        shop0_.name as name4_11_ 
    from
        shop shop0_ 
    where
        (
            shop0_.deleted = false
        )
```

## 상속 관계의 Entity 의 경우

Soft Delete 를 하려는 데 Entity 가 상속 관계인 경우가 있다.

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
@DiscriminatorColumn
public abstract class Shop {

}
```

```java
@Entity
@DiscriminatorValue("C")
public class Restaurant extends Shop {

}
```

```java
@Entity
@DiscriminatorValue("R")
public class Cafe extends Shop {

}
```

이런 경우 부모클래스에만 ```@SQLDelete(sql = "UPDATE shop SET deleted = true WHERE id = ?")``` 처리를 해주면 자식 테이블에서는 실제 삭제가 일어나게 된다.

아래와 같이 각 자식 클래스에 ```@OnDelete(action = OnDeleteAction.CASCADE)``` 처리를 해줘서 막아줄 수 있다.

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
@DiscriminatorColumn
@SQLDelete(sql = "UPDATE shop SET deleted = true WHERE id = ?")
public abstract class Shop {

}
```

```java
@Entity
@DiscriminatorValue("C")
@OnDelete(action = OnDeleteAction.CASCADE)
public class Restaurant extends Shop {

}
```

```java
@Entity
@DiscriminatorValue("R")
@OnDelete(action = OnDeleteAction.CASCADE)
public class Cafe extends Shop {

}
```


## @SQLDelete 와 @Where 의 한계

모든 Entity 에 ```@SQLDelete``` 와 ```@Where``` 어노테이션을 추가하고 삭제되지 않는 데이터만 조회한다면 코드도 깔끔해지고 실수할 여지도 사라지겠지만
실무에서는 ```@SQLDelete``` 나 ```@Where``` 를 거의 사용하지 않는다. 
왜냐하면 삭제된 데이터에 대한 정보를 조회하거나, 삭제된 데이터를 포함한 목록을 조회하는 등 디폴트로 추가한 조건을 제외해야 하는 일이 빈번하게 일어나기 때문이다. 
그렇기 때문에, ```@SQLDelete``` 나 ```@Where``` 를 사용하는 것 보다는, 번거롭더라도 조회 쿼리마다 삭제 여부 조건을 추가해 원하는 데이터만 조회하는 것을 권장한다.


## Reference

https://velog.io/@max9106/JPA-soft-delete