# mybatis 에서 Dynamic Router 설정하기

데이터베이스를 통한 데이터 조회 시 가장 많은 자원을 소모하는 곳이 바로 서버와 데이터베이스를 연결하는 부분이다.
데이터를 조회할 때마다 데이터베이스와 연결을 맺어야 하는 자원 낭비를 줄이기 위해 스프링에서는 어플리케이션 기동 시 
데이터베이스 커넥션 객체를 스프링 빈(Bean) 객체로 만들고 미리 연결을 맺어놓는다. 그리고 서버에서 데이터베이스와 연결할 때 미리 맺어놓은 연결을 사용해
최소한의 자원 소모로 효율적인 데이터베이스와의 연결을 가능하게 한다.

하나의 데이터베이스만을 연결해야 하는 상황에서는 기본적으로 

다음 예제에 작성된 소스코드들의 기본적인 매커니즘은 다음과 같다.

1. 서비스 기동 시 커넥션 객체(DataSource)로 등록할 정보를 조회한다.
2. 조회한 정보들로 커넥션 객체를 만들고 Map 에 담아 Key(여기서는 certkey 라는 API-Key 로 지정) 와 함께 Spring Bean 객체로 만든다. 
3. 클라이언트의 요청이 들어오면 Key 값에 맞는 커넥션 객체를 조회하고, 커넥션과 연결한다.

#### RoutingDataSourceContextHolder

```java
public class RoutingDataSourceContextHolder {
	
	private static ThreadLocal<String> threadLocal = new ThreadLocal<>();
	
	public static void setRoutingDataSourceContextHolder(String dataSourceType) {
		threadLocal.set(dataSourceType);
	}
	
	public static String getRoutingDataSourceContextHolder() {
		return threadLocal.get();
	}
	
	public static void clearRoutingDataSourceContextHolder() {
		threadLocal.remove();
	}
	
}
```


#### RoutingDataSourceConfig

````java
@Configuration
@MapperScan(value = { "org.example.my.mapper","org.example.your.mapper" }, sqlSessionFactoryRef = "routingSqlSessionFactory")
@RequiredArgsConstructor
public class RoutingDataSourceConfig {

	private final RoutingDataSourceUtil util;

	@Bean
	public DataSource routingDataSource() throws ApiException {
		AbstractRoutingDataSource routingDataSource = new RoutingDataSource();
		Map<Object, Object> targetDataSources = util.loadRoutingDataSource();
		routingDataSource.setTargetDataSources(targetDataSources);
		return routingDataSource;
	}

	@Bean
	public SqlSessionFactory routingSqlSessionFactory(@Qualifier("routingDataSource") DataSource dataSource,
			ApplicationContext applicationContext) throws Exception {
		SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
		sqlSessionFactoryBean.setDataSource(dataSource);
		sqlSessionFactoryBean.setMapperLocations(applicationContext.getResources("classpath:mapper/web/**.xml"));
		Resource myBatisConfig = new PathMatchingResourcePatternResolver().getResource("classpath:mybatis-config.xml");
		sqlSessionFactoryBean.setConfigLocation(myBatisConfig);
		return sqlSessionFactoryBean.getObject();
	}

	@Bean
	public SqlSessionTemplate routingSqlSessionTemplate(
			@Qualifier("routingSqlSessionFactory") SqlSessionFactory sqlSessionFactory) {
		return new SqlSessionTemplate(sqlSessionFactory);
	}

	@Bean
	public PlatformTransactionManager routingTransactionTemplate(
			@Qualifier("routingDataSource") DataSource dataSource) {
		return new DataSourceTransactionManager(dataSource);
	}
}
````

#### RoutingDataSource

```java
@RequiredArgsConstructor
public class RoutingDataSource extends AbstractRoutingDataSource {
    
	private final RoutingDataSourceUtil util;
    
	private final ExternalResource externalResource;
	
	@Override
	protected Object determineCurrentLookupKey() {
		return RoutingDataSourceContextHolder.getRoutingDataSourceContextHolder();
	}
	
	@Override
	protected DataSource determineTargetDataSource() {
		DataSource dataSource = null;
		try {
			dataSource = super.determineTargetDataSource();
			DataSource altDataSource = util.getDataSource(RoutingDataSourceContextHolder.getRoutingDataSourceContextHolder());
			if(!compareDataSource(dataSource, altDataSource)) {
				dataSource = setDataSource();
			}
		} catch (IllegalStateException e) {
			dataSource = setDataSource();
		} catch(Exception e) {
			System.out.println("determineTargetDataSource Exception : " + e.getMessage());
		}
		return dataSource;
	}
	
	private DataSource setDataSource() {
		DataSource dataSource = null;
		try {
			this.setTargetDataSources(util.loadRoutingDataSource());
			this.afterPropertiesSet();

			dataSource = super.determineTargetDataSource();
		} catch (ApiException e1) {
			System.out.println("targetDataSource exception");
		} catch (IllegalStateException e3) {
			System.out.println("illegalStateException occured : " + e3.getMessage());
		} catch (Exception e2) {
			System.out.println("determineTargetDataSource exception occured : " + e2.getMessage());
		}
		
		return dataSource;
	}
	
	private boolean compareDataSource(DataSource one, DataSource other) throws SQLException {
		if(one == null || other == null) 
			return false;
		HikariDataSource oneProp = (HikariDataSource)one;
		HikariDataSource otherProp = (HikariDataSource)other;

		if(!oneProp.getJdbcUrl().equals(otherProp.getJdbcUrl())
				|| !oneProp.getUsername().equals(otherProp.getUsername())
				|| !oneProp.getPassword().equals(otherProp.getPassword()))
			return false;
		return true;
	}
}
```

#### RoutingDataSourceUtil

```java
@Component
@Slf4j
@RequiredArgsConstructor
public class RoutingDataSourceUtil {
    
    private final DatabaseInfoMapper databaseInfoMapper;
    
	private final CryptoAriaService cryptoAriaService;

	public Map<Object, Object> loadRoutingDataSource() throws ApiException {
		Map<Object, Object> targetDataSources = new HashMap<>();
		try {
			List<DatabaseInfo> apiUseInfo = databaseInfoMapper.getDatabaseInfoList(null);
			for (ApiUseDetailType info : apiUseInfo) {
				String driverClassName = "org.postgresql.Driver";
				String crtfckey = info.getCrtfckey();
				String jdbcUrl = info.getDatabaseUrl();
				String username = info.getDatabaseUsernm();
				String password = info.getDatabasePassword();
				targetDataSources.put(crtfckey, routingDataSource(info.getCrtfckey(), driverClassName, jdbcUrl,
						username, cryptoAriaService.decryptData(password)));
			}
            
		} catch (SQLException e) {
			throw new ApiException(CodeType.ERROR_ETC, "datasource 설정 실패");
		} catch (Exception e) {
			throw new ApiException(CodeType.ERROR_ETC, "datasource 설정 실패");
		}
		return targetDataSources;
	}
	
	public DataSource getDataSource(String crtfckey) throws SQLException, Exception {
		ApiUseDetailType info = apiUseMapper.selectByCrtfckey(crtfckey);
		if(info == null)
			return null;
		return routingDataSource("org.postgresql.Driver", info.getDatabaseUrl(), info.getDatabaseUsernm(), cryptoAriaService.decryptData(info.getDatabasePassword()));
	}

	private DataSource routingDataSource(String driverClassName, String jdbcUrl, String username,
			String password) {
		HikariDataSource dataSource = new HikariDataSource();
		dataSource.setDriverClassName(driverClassName);
		dataSource.setJdbcUrl(jdbcUrl);
		dataSource.setUsername(username);
		dataSource.setPassword(password);
		dataSource.setAutoCommit(false);
		return dataSource;
	}

}
```