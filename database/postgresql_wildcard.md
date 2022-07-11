# PostgreSQL Wildcard

PostgreSQL 을 사용할 때, where 절에 like 문을 사용하는 경우 ```%```, ```_```2가지의 와일드카드를 사용할 수 있다.
와일드카드는 문자열 시작, 중간, 끝 어느 위치에 있어도 상관없으며 해당 위치에 맞게 필터링이 적용된다.

### %

길이가 0인 문자열을 포함하여 모든 문자열의 일치 여부를 판단한다.
예를 들어 다음과 같은 쿼리를 실행시킬 수 있다.

```sql
select
    user_id,
    user_name
from users
where user_id like 'test%';
```

위의 쿼리는 사용자 아이디가 "test" 로 시작하는 모든 데이터를 조회하는 쿼리이다.   
test, test1, test2, tester 등의 결과가 조회된다.

문자열 앞과 중간에서도 사용할 수 있으며, 여러 개도 사용이 가능하다.

```sql
select
    user_id,
    user_name
from users
where user_id like 'te%st';
```

```Example = test, te1st, testConst...```

```sql
select
    user_id,
    user_name
from users
where user_id like '%test%';
```

```Example = test, test1, 1test, mytester...```

### _

하나의 문자열의 일치 여부를 판단한다. 사용법은 '%' 과 동일하다.


'%' 와 함께 사용할 수도 있다.

모든 문자열 + test + 문자열 1개

### Escape

``` sql
LIKE [pattern] escape [escape_character]
```


## Reference

https://www.techonthenet.com/postgresql/like.php