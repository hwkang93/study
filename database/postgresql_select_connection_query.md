# PostgreSQL 에서 커넥션 조회하는 쿼리

```sql
-- 총 커넥션 수
select * from pg_catalog.pg_settings where name = 'max_connections';

-- 현재 사용중인 커넥션 수
select count(*) from pg_catalog.pg_stat_activity;
```