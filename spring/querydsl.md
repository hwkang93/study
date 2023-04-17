# QueryDSL

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

