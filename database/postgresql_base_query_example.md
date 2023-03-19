# PostgreSQL 자주 사용하는 쿼리 모음

### 테이블 인덱스 조회

```postgresql
select
    t.relname as table_name,
    i.relname as index_name,
    a.attname as column_name
from
    pg_class t,
    pg_class i,
    pg_index ix,
    pg_attribute a
where
    t.oid = ix.indrelid
    and i.oid = ix.indexrelid
    and a.attrelid = t.oid
    and a.attnum = ANY(ix.indkey)
    and t.relkind = 'r'
    and t.relname like '테이블 명'
order by
    t.relname,
    i.relname;
```

### 커넥션 조회

```postgresql
-- 총 커넥션 수
select * from pg_catalog.pg_settings where name = 'max_connections';

-- 현재 사용중인 커넥션 수
select count(*) from pg_catalog.pg_stat_activity;
```