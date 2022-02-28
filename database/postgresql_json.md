# PostgreSQL 에서 JSON 타입 사용하기

PostgreSQL 에서 JSON 타입의 데이터를 사용하는 방법과 처리하는 방법에 대한 정리이다.

PostgreSQL 은 9.2 버전부터 JSON 타입을 지원하기 시작했으며, JSON 객체를 처리하기 위한 연산자 및 함수들을 제공한다.
공식 홈페이지에 나와있는 설명 및 실습을 따라해보고 정리해보았다. 

## 실습

### 실습 환경

먼저 실습 환경이다.
버전에 따라 제공하는 함수가 다를 수 있으니 참고하자.

만약 PostgreSQL 이 설치되어있지 않다면, [공식 홈페이지](https://www.postgresql.org/download/) 에서 다운을 받아 실습 준비를 한다.

- PostgreSQL 14 
- PgAdmin4

### 테이블 생성

주문 아이디와 주문 정보를 가진 테이블을 하나 만든다. 
여기서 데이터 추가(INSERT) 을 편하게 하기 위해 **ID 타입은 ```serial``` 로 해주고, 주문 정보는 JSON 타입**으로 만들어준다.

```postgresql
CREATE TABLE orders (
    id serial NOT NULL PRIMARY KEY,
    info json NOT NULL
);
```

### 데이터 추가

실습 데이터를 추가한다.
```id``` 는 ```serial``` 타입이기 때문에 json 구조로 된 ```info``` 데이터만 추가하면 된다.

```postgresql
INSERT INTO orders (info)
VALUES ('{ "customer": "John Doe", "items": {"product": "Beer","qty": 6}}'),
       ('{ "customer": "Lily Bush", "items": {"product": "Diaper","qty": 24}}'),
       ('{ "customer": "Josh William", "items": {"product": "Toy Car","qty": 1}}'),
       ('{ "customer": "Mary Clark", "items": {"product": "Toy Train","qty": 2}}'),
       ('{ "customer": "Kang"}');
```

JSON 형식은 ```Key : Value``` 의 구조를 가진다.
정상적인 JSON 형식이 아닌 데이터를 추가하려고 하면 PostgreSQL 에서 오류를 발생시킨다.

```postgresql
INSERT INTO orders(info)
VALUES ('Choi');
```
- 결과
```
ERROR: 오류:  json 자료형에 대한 잘못된 입력 구문
LINE 2: VALUES ('Choi');
                ^
DETAIL:  잘못된 토큰: "Choi"
CONTEXT:  JSON 자료, 1 번째 줄: Choi


SQL state: 22P02
Character: 34
```

### SELECT

JSON 데이터를 조회하는 것은 기존 ```SELECT``` 쿼리와 유사하다.

```postgresql
SELECT INFO FROM ORDERS;
```

JSON 데이터의 Key 값을 통해 데이터를 조회하는 경우를 위해 PostgreSQL 에서는 ```->``` 와, ```->>``` 두 가지 연산자를 제공한다.
- ```->``` : JSON 타입의 데이터를 반환한다.
- ```->>``` : TEXT 타입의 데이터를 반환한다.

```info``` 컬럼에서 ```customer``` 만 JSON 타입의 결과로 조회하고 싶은 경우 다음처럼 쿼리를 작성한다.

```postgresql
SELECT INFO -> 'customer' AS customer
FROM ORDERS;
```

TEXT 타입으로 리턴받고 싶은 경우에는 다음과 같이 작성한다.

```postgresql
SELECT INFO ->> 'customer' AS customer
FROM ORDERS;
```

```-> ``` , ```->>``` 연산자는 연결하여 사용할 수도 있다.
예를 들어, info 컬럼의 items 안에 있는 product 목록을 출력하고 싶은 경우에는 다음과 같이 쿼리를 작성한다.

```postgresql
SELECT INFO -> 'items' ->> 'product' AS product
FROM ORDERS;
```

### WHERE

WHERE 절에서도 ```->```  , ```->>``` 연산자를 사용할 수 있는데, 사용법은 ```SELECT``` 절에서 사용했던 것과 유사하다.

다음은 ```customer``` 가 Kang 인 사람을 조회하는 쿼리이다.

```postgresql
SELECT INFO
FROM ORDERS
WHERE INFO ->> 'customer' = 'Kang'
```

여기서 염두에 둬야 할 것은 ```->>``` 연산자를 사용했다는 것인데,
'Kang' 라는 **문자열** 과 비교를 해야 하기 때문에 문자열을 리턴하는 ```->>``` 연산자를 사용했다.

그럼 숫자로 비교하기 위해서는 어떻게 해야 할까? 

**숫자로 비교하기 위해서는 형변환 작업이 필요하다.**

다음은 ```qty``` 가 2 이상인 주문을 조회하는 쿼리이다.

```postgresql
SELECT INFO
FROM ORDERS
WHERE CAST (INFO -> 'items' ->> 'qty' AS INTEGER) > 2
```

쿼리를 보면 알 수 있듯이 먼저 JSON 객체에서 ```qty``` 값을 뽑아낸 다음, 
INTEGER 타입으로 형변환을 하고, 조회할 값과 비교한다.  








## Reference

[PostgreSQL JSON - PostgreSQL Tutorial](https://www.postgresqltutorial.com/postgresql-json/)