

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
    
	private final CryptoAriaService cryptoAriaService;

	public Map<Object, Object> loadRoutingDataSource() throws ApiException {
		Map<Object, Object> targetDataSources = new HashMap<>();
		try {
			List<ApiUseDetailType> apiUseInfo = apiUseMapper.list(null);
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
		return routingDataSource(crtfckey, "org.postgresql.Driver", info.getDatabaseUrl(), info.getDatabaseUsernm(), cryptoAriaService.decryptData(info.getDatabasePassword()));
	}

	private DataSource routingDataSource(String crtfckey, String driverClassName, String jdbcUrl, String username,
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