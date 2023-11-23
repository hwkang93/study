# QueryDSL

JPARepository 객체를 확장하여



## QueryDSL 의 장점

- 가독성 : 문자열로 작성하는 SQL 쿼리는 코드로 짜여진 QueryDSL 에 비해 가독성이 떨어진다.
- 유지보수성 : 자바 소스코드로 쿼리를 작성하기 때문에 컴파일 시점에 오타 등의 오류를 찾을 수 있다. 또한 컬럼 변경과 같은 쿼리 변경 시에도 컴파일을 통해 수정 범위를 줄일 수 있다.
- 



## 설정

maven 기준

### pom.xml

```xml
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-apt</artifactId>
</dependency>
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-jpa</artifactId>
</dependency>



<!-- add plugin -->
<plugin>
<groupId>com.mysema.maven</groupId>
<artifactId>apt-maven-plugin</artifactId>
<version>1.1.3</version>
<executions>
    <execution>
        <goals>
            <goal>process</goal>
        </goals>
        <configuration>
            <outputDirectory>src/main/generated</outputDirectory>
            <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
        </configuration>
    </execution>
</executions>
</plugin>
```

### Java Configuration

```QueryDslConfig.java```

```
@Configuration
public class QueryDslConfig {

    @PersistenceContext
    private EntityManager entityManager;

    @Bean
    public JPAQueryFactory jpaQueryFactory() {
        return new JPAQueryFactory(entityManager);
    }

}
```


## Reference

https://velog.io/@youngerjesus/%EC%9A%B0%EC%95%84%ED%95%9C-%ED%98%95%EC%A0%9C%EB%93%A4%EC%9D%98-Querydsl-%ED%99%9C%EC%9A%A9%EB%B2%95