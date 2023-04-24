#JPA Custom Generator 구현 일지

> 전자정부 프레임워크의 PK 만드는 방법을 JPA Entity 클래스에 적용시키면서 생겼던 문제에 대해 정리해봤다.

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

그래서 ```org.hibernate.id.enhanced.TableGenerator``` 를 확장한 클래스를 구현하기로 결정했다.


## KeyGenerator.java 구현하기

```org.hibernate.id.enhanced.TableGenerator``` 클래스를 확장하기 전에 재정의(Override)해야 할 메소드들을 정리했다.
이미 잘 만들어진 클래스이기 때문에 최소한의 기능만 수정 및 확장을 해야겠다는 마음이 컸기 때문이다.

그리고 두 가지 메소드를 재정의하기로 결정했다.

### generate

먼저, PK 를 채번하는 generate 의 메소드는 재정의가 필수였다. 재정의한 generate 메소드의 기능은 다음과 같다.

1. super 클래스의 generate 메소드를 호출해 PK 를 채번한다.
2. prefix, 자릿수, 비어있는 자릿수에 채울 문자열을 통해 새로운 PK 를 만들어 리턴한다.


### configure

다음으로는 추가로 필요한 파라미터들을 정의하고, 개발자들의 편의를 위해 디폴트 값을 세팅할 configure 함수를 재정의했다.
추가로 필요한 파라미터는 다음과 같다.

1. prefix : 접두사
2. fill : 비어있는 자릿수에 채울 문자열
3. length : 자릿수

예를 들어, prefix="MY", fill="0", length="12" 파라미터를 추가로 받았고 TableGenerator 에서 채번한 값이 "7" 이라고 한다면
결과값은 "MY0000000007" 이 되어야 한다.

### 구현

```java
@Override
public Serializable generate(SharedSessionContractImplementor session, Object obj) {
    Serializable generate = super.generate(session, obj);
    Integer next = Integer.parseInt(generate.toString());
    String format = "%"+fill+(Integer.parseInt(length)-prefix.length())+"d";

    return prefix + "" + String.format(format, next);
}

@Override
public void configure(Type type, Properties params, ServiceRegistry serviceRegistry) throws MappingException {
    //Default Value Setting
    params.put("table_name", "ID 를 관리하는 테이블 명");
    params.put("value_column_name", "채번할 컬럼 명");
    params.put("segment_column_name", "채번할 컬럼의 PK");

    this.prefix = params.get("prefix").toString();
    this.fill = StringUtils.isEmpty(params.get("fill").toString()) ? FILL_DEFAULT : params.get("fill").toString();
    this.length = StringUtils.isEmpty(params.get("length").toString()) ? LENGTH_DEFAULT : params.get("length").toString();

    super.configure(type, params, serviceRegistry);
}
```

위에서 정리한 요구사항만을 재정의한 메소드의 구현체이다.


## 뜻하지 않은 문제 발생

generate 메소드를 보면 리턴을 ```prefix + "" + String.format(format, next);``` 으로 한 것을 볼 수 있다. 이것은 @Id 컬럼을 String 으로 하더라도
매핑이 잘 되겠지? 라고 안일하게 생각을 한 내 잘못이었다.

테스트코드를 작성하고 돌려보니 에러가 발생했다.

```
Unknown integral data type for ids : java.lang.String
```

@Id 필드는 String 을 가질 수 없다는 오류였다.
로그를 조금 더 자세히 보니 에러가 난 위치는 ```Serializable generate = super.generate(session, obj);``` 라는 걸 알 수 있었다.

아, 채번한 값을 주입하는 시점에 오류가 난 게 아니고 채번할 때 이미 @Id 컬럼의 자료형을 알고 있구나.

TableGenerator.java 파일을 조금 더 자세히 보니 해결 방법을 찾을 수 있었다. (꽤 오랜 시간이 걸렸다.)

TableGenerator.java 에서 채번을 할 때 optimizer.generate 함수를 호출한다.
optimizer 는 최초 TableGenerator 가 로딩될 때 configure 메소드에 의해 정의되는데, 이 시점에 리턴 타입을 결정한다.
리턴 타입은 identifierType 필드에 의해 결정이 되며 (identifierType.getReturnedClass()) identifierType 필드는 configure 메소드의 첫 번째 인자였다.
(디폴트 값이 @Id 필드의 자료형)

해결을 위해 재정의한 configure 메소드를 수정해줬다

```java
@Override
public void configure(Type type, Properties params, ServiceRegistry serviceRegistry) throws MappingException {
    Type longType = new LongType();

    //Default Value Setting
    params.put("table_name", "ID 를 관리하는 테이블 명");
    params.put("value_column_name", "채번할 컬럼 명");
    params.put("segment_column_name", "채번할 컬럼의 PK");

    this.prefix = params.get("prefix").toString();
    this.fill = StringUtils.isEmpty(params.get("fill").toString()) ? FILL_DEFAULT : params.get("fill").toString();
    this.length = StringUtils.isEmpty(params.get("length").toString()) ? LENGTH_DEFAULT : params.get("length").toString();

    super.configure(longType, params, serviceRegistry);
}
```