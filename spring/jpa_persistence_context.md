# JPA 영속성 컨텍스트에 대하여

JPA 에서 가장 중요한 것 두가지

1. 객체와 관계형 데이터베이스 매핑 (Object Relational Mapping)
2. 영속성 컨텍스트

## EntityManagerFactory EntityManager

- EntityManagerFactory 는 어플리케이션을 최초 기동할 때 하나만 만들어지는 것이다.
- 유저가 요청을 할 때마다 (쓰레드 당) 하나의 EntityManager 가 생성된다.
- EntityManager 는 내부적으로 데이터베이스 커넥션을 사용해서 데이터베이스의 데이터를 사용한다.



## 영속성 컨텍스트

**엔티티를 영구 저장하는 환경** 이라는 뜻

EntityManager.persist(entity);

 -> persist 는 entity 를 영속성 컨텍스트에 저장하는 메소드
 
- 논리적인 개념 (눈에 보이지 않는다.)
- EntityManager 를 통해 영속성 컨텍스트에 접근한다.

### 이점

#### 1차 캐시

```@Id``` 필드가 Key , 엔티티 객체가 Value 로 1차 캐싱을 한다.

예를 들어 다음과 같은 엔티티가 있고,

```java
Member memeber = new Member();
member.setId("member1");
member.setUsername("회원1");
```

영속성 컨텍스트에 저장했을 때

```java
//1차 캐시에 저장됨
entityManger.persist(member);
```

여기서 ID(PK) 가 ```member1``` 인 객체를 조회하는 함수를 날리면 JPA 는 먼저 데이터베이스가 아닌 캐싱된 데이터를 조회한다.

```java
Member findMember = entityManger.find(Member.class, "member1");
```

만약 1차 캐시에 저장되지 않은 member2 를 조회하려고 한다면,

```java
Memeber findMember2 = entityManager.find(Member.class, "member2");
```

먼저 1차 캐시에서 데이터를 조회하고, 결과가 없으니 데이터베이스에서 조회한 후 그 결과를 1차 캐시에 저장하고 반환한다. 

1차 캐시는 메모리 사용 측면에서 바라봤을 때 그렇게 큰 도움이 되지는 않는다. 
하나의 트랜잭션이 생성될 때 하나의 EntityManger 가 만들어지고 트랜잭션이 종료될 때, EntityManager 는 소멸하고 1차 캐시도 함께 소멸된다.

즉 


2. 동일성 (identity) 보장
3. 트랜잭션을 지원하는 쓰기 지연 (transactional write-behind)
4. 변경 감지(Dirty checking)
5. 지연 로딩(Lazy loading)




# 엔티티의 생명 주기

- 비영속 (new/transient) : 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태

```java
//객체를 생성한 상태(비영속)
Member memeber = new Member();
member.setId("member1");
member.setUsername("회원1");
```

- 영속 (managed) : 영속성 컨텍스트에 관리되는 상태

```java
//객체를 생성한 상태(비영속)
Member memeber = new Member();
member.setId("member1");
member.setUsername("회원1");

EntityManger em = entityManagerFactory.createEntityManager();
em.getTransaction().begin();

//객체를 저장한 상태 (영속)
em.persist(member);
```

영속성 컨텍스트에 관리되는 데이터들은 transaction.commit() 메소드가 실행되는 시점에 데이터베이스로 쿼리가 날아간다.

- 준영속 (detached) : 영속성 컨텍스트에 저장되었다가 분리된 상태

```java
//회원 엔티티를 영속성 컨텍스트에서 분리한 상태
em.detach(member);
```

- 삭제 (removed) : 삭제된 상태

```java
//객체를 삭제한 상태(삭제) -> 실제로 DB DELETE 쿼리를 날린다.
em.remove(member);
```

## Reference

인프런 강의 