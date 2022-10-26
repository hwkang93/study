# View

```postgresql
CREATE [ OR REPLACE ] [ TEMP|TEMPORARY ] VIEW name [(column_name[...])] [WITH (view_option_name [ =view_option_value ] [...])]
AS query
```

쿼리의 결과값을 바탕으로 생성된 가상 테이블을 뷰라고 한다.

뷰는 테이블과 비슷하지만, 물리적인 공간에 생성되는 것이 아닌 뷰를 호출할 때 뷰에 정의된 쿼리를 실행하고 그 결과를 리턴하는 방식을 취한다.

## 예시

### View 생성

```postgresql
CREATE OR REPLACE VIEW public.users_view
AS
select
    *
from
    public.users;
```

### View 삭제

```postgresql
drop view public.users_view;
```


## Reference

https://postgresql.kr/docs/11/sql-createview.html

[코딩팩토리 - [DB기초] 뷰(View)란 무엇인가? + 간단한 예제](https://coding-factory.tistory.com/224)