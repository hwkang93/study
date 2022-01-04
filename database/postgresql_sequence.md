# PostgresSQL 에서 시퀀스 관리하기

### 생성

```sql
CREATE SEQUENCE user_seq;
```

### 삭제

```sql
DROP SEQUENCE user_seq;
```

### 현재값 조회

```sql
SELECT currval('user_seq');
```

### 다음값 조회
```sql
SELECT nextval('user_seq');
```

### 시퀀스 사용 예제
```sql
insert into user (
    id, name, age
) values (
    nextval('user_seq'), 'kang', 30
);
```

### 시퀀스 목록 조회

```sql
select 
       n.nspname as sequence_schema, 
       c.relname as sequence_name, 
       u.usename as owner 
from pg_class c 
    join pg_namespace n on n.oid = c.relnamespace 
    join pg_user u on u.usesysid = c.relowner 
where c.relkind = 'S' and u.usename = current_user;
```


## Reference

[나만의 기록들 - [PostgreSQL] 시퀀스(Sequence) 사용법 (nextval, currval, setval)](https://mine-it-record.tistory.com/341)