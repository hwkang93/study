# PostgreSQL Wildcard

PostgreSQL 을 사용할 때, where 절에 like 문을 사용하는 경우 ```%```, ```_```2가지의 와일드카드를 사용할 수 있다.
와일드카드는 문자열 시작, 중간, 끝 어느 위치에 있어도 상관없으며 해당 위치에 맞게 필터링이 적용된다.

## % (Percent)

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

## _ (Underscore)

하나의 문자열의 일치 여부를 판단한다. 사용법은 '%' 과 동일하다.

```sql
select
    user_id,
    user_name
from users
where user_id like 'test_';
```

```Example = test1, test2, test3 ...```

```sql
select
    user_id,
    user_name
from users
where user_id like '_test';
```

```Example = 1test, 2test, 3test ...```

'%' 와 함께 사용할 수도 있다.

```sql
select
    user_id,
    user_name
from users
where user_id like '%test_';
```

모든 문자열 + test + 문자열 1개

```Example : test1, test2, test3, mytest1```

### Escape

``` sql
LIKE [pattern] escape [escape_character]
```

**와일드카드를 리터럴 문자로 조회하고자 하는 경우에는 이스케이프 문자를 사용하여 조회하고자 하는 문자가 와일드카드가 아닌 리터럴 문자라는 것을 명시해줘야 한다.**
PostgreSQL 에서는 디폴트 이스케이프 문자로 ``` \ ``` 를 제공하고 있으며, 상황에 따라 커스타마이징을 할 수 있다.

예를 들어, 사용자 아이디가 ```test_1``` 이고 이 사용자를 조회하려고 한다.

```sql
select
    user_id,
    user_name
from users
where user_id like 'test_1';
```

이 경우 ```WHERE user_id LIKE 'test_1'``` 라는 쿼리문으로는 원하는 결과를 얻을 수 없다.
위의 쿼리로는 test_1 뿐만 아니라, test01, test21 과 같이 test 문자열과 1 문자열 사이에 하나의 문자열이 있는 모든 사용자 아이디가 조회될 것이다.
 
이러한 경우 이스케이프를 사용하여 원하는 결과값을 얻을 수 있는데, 아래와 같은 쿼리를 예로 들 수 있다.

```sql
select
    user_id,
    user_name
from users
where user_id like 'test\_1';
```

위의 쿼리로 조회하면 ```test_1``` 을 아이디로 가진 사용자만 출력될 것이다.

이스케이프 문자를 재정의할 수도 있다. 예를 들어 ``` \ ``` 가 아닌, ```!``` 을 이스케이프 문자로 정의하고 싶다면, 다음과 같이 작성하면 된다.

```sql
select
    user_id,
    user_name
from users
where user_id like 'test!_1' escape '!';
```

원하는 결과를 조회할 수 있을 것이다.

## Reference

https://www.techonthenet.com/postgresql/like.php