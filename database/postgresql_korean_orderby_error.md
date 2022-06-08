# PostgresSQL 한글 OrderBy 안되는 오류

```postgresql
select datname, datcollate from pg_database;
```

datcollate 가 ```en_US.UTF-8``` 로 돼있으면 수정이 필요하다.

방법 1 DB 를 다시 생성

기존 테이블 덤프 뜨고 테이블 새로 만들고 덤프뜬 데이터들 새로 만든 테이블에 밀어넣기

테이블 생성 예시)

```
CREATE DATABASE [DATABASE_NAME] TEMPLATE template0 LC_COLLATE 'C'; 
```

```
CREATE DATABASE [DATABASE_NAME] TEMPLATE template0 LC_COLLATE 'ko_KR.utf8';
```

C(POSIX) 옵션의 경우 아스키코드 값으로 비교하는데 한글 영어 혼합의 경우 정렬이 정상적으로 되지 않을 수 있으므로
가급적 ko_KR.utf8 로 진행한다.

방법 2 쿼리 수정

order by 절을 다음과 같이 수정한다.

> 대소문자를 틀리지 않게 유의해야 한다.

```postgresql
select * from users order by name collate "C" desc;
```

```postgresql
select * from users order by name collate "ko_KR.utf8" desc;
```

## Reference
[[AWS]RDS postgresql 한글정렬 문제 - jeong3320.log](https://velog.io/@jeong3320/AWSRDS-postgresql-%ED%95%9C%EA%B8%80%EC%A0%95%EB%A0%AC-%EB%AC%B8%EC%A0%9C)
