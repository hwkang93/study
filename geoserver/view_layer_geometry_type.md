# GeoServer 에서 View 를 발행했을 때의 Geometry 타입에 대하여

폴리곤의 중심점, 혹은 지리정보 연산 등의 결과를 view로 만들어 그 view 자체를 GeoServer에 레이어로 발행할 일이 종종 있다.
예를 들어 폴리곤의 중심점을 view 로 만드는 쿼리는 다음과 같이 작성할 수 있는데 예를 들면 아래와 같은 쿼리이다.

```sql
create view public.polygon_example_view as
select
    fid ,
    code ,
    kor_nm ,
    st_centroid(geom) as geom
from public.polygon_example;
```


위의 view 를 레이어로 발행하면 GeoServer 에서는 geometry 타입을 모르기 때문에 컬럼 타입을 Geometry 로 지정하게 된다.
이럴 때에는 view 를 생성할 때 컬럼 타입을 명시해주면 되는데, 방법은 다음과 같다.

```sql
create view public.polygon_example_view as
select
    fid ,
    code ,
    kor_nm ,
    st_centroid(geom)::geometry(Point,5186) as geom
from public.polygon_example;
```

