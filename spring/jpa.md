# JPA

- JPA : Java Persistence API
- 자바 진영의 ORM 표준 기술

**ORM**
- Object-relational mapping (객체 관계 매핑)
- 객체는 객체대로 설계
- 관계형 데이터베이스는 관계형 데이터베이스대로 설계
- ORM 프레임워크가 중간에서 매핑
- 대중적인 언어네는 대부분 ORM 기술이 존재

- JPA 는 어플리케이션과 JDBC 사이에서 동작한다

> JAVA Application -> **JPA** -> JDBC API -> SQL -> DB

## JPA 동작 - 저장

- Entity 분석 후 Insert SQL 생성
- JDBC API 사용
- 패러다임 불일치 해결

## JPA 동작 - 조회

- SELECT SQL 생성
- JDBC API 사용
- ResultSet 매핑



--------------------------------------------------------------------------

## JPA 설정하기

1. /META-INF/persistence.xml 생성

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
             xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="hello">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value="sa"/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <!-- 데이터베이스 방언을 H2 로 선택하겠다. -->
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>

            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.format_sql" value="true"/>
            <property name="hibernate.use_sql_comments" value="true"/>
            <!--<property name="hibernate.hbm2ddl.auto" value="create" />-->
        </properties>
    </persistence-unit>
</persistence>
```

2. persistence-unit name 으로 이름 지정
3. javax.persistence 로 시작 : JPA 표준 속성
4. hibernate 로 시작 : 하이버네이트 전용 속성


JPA 는 특정 데이터베이스에 종속되지 않는다.
- 가변문자 : MySQL 은
- 페이징 처리를 할 때 MySQL 은 limit , Oracle 은 ROWNUM 을 사용

## JPA 구동 방식

1. Persistence 의 설정 정보를 조회한다. (/META-INF/persistence.xml)
2. EntityManagerFactory 클래스를 생성한다.
3. 필요할 때 EntityManager 를 생성한다.

## Reference



https://milenote.tistory.com/131