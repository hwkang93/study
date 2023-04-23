전자정부 프레임워크의 PK 만드는 방법을 JPA Entity 클래스에 적용시키면서 생겼던 문제에 대해 정리해봤다.

전자정부프레임워크는 PK 를 관리하는 테이블이 있고, PK 테이블에서 값을 관리한다.
예를 들어


이것을 JPA 의 Entity 형태로 옮기면 다음과 같은 Entity 클래스를 작성할 수 있다.

```java
@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Table(name = "CMMN_BBS_NTT_INFO")
@TableGenerator(
        name="postIdGenerator",
        table="ID_SN_MANAGE",
        pkColumnName = "table_nm",
        valueColumnName = "next_sn",
        allocationSize = 1
)
public class Post extends BaseTimeEntity {
    @Id
    @GeneratedValue(strategy=GenerationType.TABLE, generator = "postIdGenerator")
    @Column(name = "post_id")
    private long postId;

    //...
```

JPA 에서 제공하는 @TableGenerator 어노테이션을 사용해, PK 테이블, PK 컬럼 명, VALUE 값을 설정해주면
게시글(Post)을 등록할 때 전자정부프레임워크의 KEY 채번 방식과 비슷한 로직으로 PK를 조회해온다.

하지만 JPA 에서 제공하는 @TableGenerator 방식은 반드시 long 타입이어야 한다.

```java
    public Serializable generate(final SharedSessionContractImplementor session, Object obj) {
        final SqlStatementLogger statementLogger = ((JdbcServices)session.getFactory().getServiceRegistry().getService(JdbcServices.class)).getSqlStatementLogger();
        final SessionEventListenerManager statsCollector = session.getEventListenerManager();
        return this.optimizer.generate(new AccessCallback() {
            public IntegralDataTypeHolder getNextValue() {
                return (IntegralDataTypeHolder)session.getTransactionCoordinator().createIsolationDelegate().delegateWork(new AbstractReturningWork<IntegralDataTypeHolder>() {
                    public IntegralDataTypeHolder execute(Connection connection) throws SQLException {
                        IntegralDataTypeHolder value = TableGenerator.this.makeValue();
			//...
                        TableGenerator.this.accessCount++;
                        return value;
                    }
                }, true);
            }

            public String getTenantIdentifier() {
                return session.getTenantIdentifier();
            }
        });
    }
```

```org.hibernate.id.enhanced.TableGenerator``` 클래스의 채번 메소드이다. 새로운 데이터를 추가할 때 반드시 숫자형 타입이어야 하는 것을 알 수 있다.(IntegralDataTypeHolder)

그럼 전자정부프레임워크의 채번 방식처럼 접두사를 포함한 N자리 문자열에 비어있는 자리를 0과 같은 값으로 채우려면 어떻게 해야 할까?

문제를 이렇게 정리해볼 수 있겠다.

전자정부프레임워크의 채번 방식을 JPA 에 가져와야 하는데 다음과 같은 이슈사항이 있다.
- 기본적으로 전자정부프레임워크의 채번 방식은 @TableGenerator 와 같은 로직을 가지고 있다
  (PK 테이블에서 채번 -> PK 테이블 업데이트 -> 채번한 값 리턴)
- 하지만 @TableGenerator 를 사용하려면 @Id 의 타입이 반드시 숫자형이어야 한다.(int, long 등)
- 내가 하고자 하는 것은 채번한 값에 접두사를 붙이고, 비어있는 자릿수만큼 0으로 채우고 싶다.
- 그러기 위해서는 @Id의 타입이 반드시 문자열이어야 한다.

여러 글들을 찾아봤고 대부분의 경우 @GenericGenerator 를 이용해 커스텀한 Generator 클래스를 만들고 필요한 파라미터를 받아 로직을 직접 구현했다.
하지만 이 프로젝트에서 선택한 채번 방식은 @TableGenerator 와 거의 로직이 일치하는데,
굳이 잘 만들어진 클래스를 두고 새로운 클래스를 만들 필요도 없다고 생각했고, 더 잘 만들 자신도 없었다.