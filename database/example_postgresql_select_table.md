# PostgreSQL 테이블 목록 조회 쿼리

다음은 PostgreSQL 에서 테이블 목록을 조회할 수 있는 쿼리이다

```sql
SELECT * FROM pg_catalog.pg_tables;
```

코멘트까지 함께 조회할 수 있는 쿼리는 다음과 같다.

```sql
SELECT 
	C.RELNAME as "TABLE_NAME", 
	OBJ_DESCRIPTION(C.OID) as "TABLE_COMMENT"
FROM PG_CATALOG.PG_CLASS C 
INNER JOIN PG_CATALOG.PG_NAMESPACE N ON C.RELNAMESPACE=N.OID 
WHERE C.RELKIND = 'r'
-- AND NSPNAME = '스키마명'
-- AND RELNAME = '테이블명'
```

### 참고

####PG_CATALOG.PG_CLASS.RELKIND 구분자

RELKIND 컬럼은 테이블 구분코드이다. 주관적으로 자주 사용하는 값은 r, v, m 정도가 있었다.

- **r = ordinary table** 
- i = index 
- S = sequence 
- t = TOAST table 
- **v = view**
- **m = materialized view**
- c = composite type 
- f = foreign table 
- p = partitioned table 
- I = partitioned index



## Reference

https://www.postgresql.org/docs/current/catalog-pg-class.html