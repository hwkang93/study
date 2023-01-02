# PostGIS(PostgreSQL) 에서 테이블 별 Geometry 정보 확인하는 방법

다음 쿼리는 PostgreSQL 에서 테이블 별 Geometry 타입과, Geometry Column 명을 조회할 수 있는 쿼리이다.
Geometry 컬럼이 존재하는 테이블만 출력되며 기본적인 테이블 정보(catalog, schema, table 명 등)도 함께 조회가 가능하다.

```sql
select * 
from public.geometry_columns
where f_table_schema = 'my_schema';
```

### 참고

테이블의 comment 까지 함께 출력하는 쿼리를 작성하는 방법이다.   
(언젠가 사용할 일이 있을 것 같아..)

```sql
select
	c.relname as "table_name" ,
	obj_description(c."oid") as "table_comment",
	g.type
from pg_catalog.pg_class c
inner join pg_catalog.pg_namespace n 
    on c.relnamespace = n."oid"
left outer join public.geometry_columns g 
    on nspname = g.f_table_schema 
   and g.f_table_name = c.relname
where c.relkind = 'r'
  and nspname = 'my_schema'
 order by 1 desc;
```