# PostGIS 의 공간(Spatial) 메서드 모음

PostGIS 에서 제공하는 공간 메서드를 사용하기 위해서는 기본적으로 데이터 타입이 Geometry 인 컬럼이 있어야 한다.
아래 예시들의 파라미터 타입 혹은 결과 타입이 Geometry 인 것들이 데이터 타입이 Geometry 인 컬럼이다.

### ST_CENTROID

**Geometry ST_Centroid(Geometry geometry)**

- Geometry 의 중심 좌표를 조회한다.

### ST_X & ST_Y

**Float ST_X(Geometry point)** & **Float ST_Y(Geometry point)**

- Geometry 의 X 좌표(또는 Y 좌표)를 조회한다.
- Geometry 중 **Point** 에서만 메소드를 사용할 수 있다.

### ST_SRID

**Integer ST_SRID(Geometry)**

- Geometry 의 좌표계를 조회한다.

```sql
SELECT ST_SRID(ST_GeomFromText('POINT(-71.1043 42.315)',4326));

-- result : 4326
```

### ST_ASTEXT

**Text ST_ASTEXT(Geometry geometry)**   
**Text ST_ASTEXT(Geometry geometry, Integer maxDecimalDigits)**   
**Text ST_ASTEXT(Geography geography)**   
**Text ST_ASTEXT(Geography geography, Integer maxDecimalDigits)**

- Geometry 또는 Geography 값을 OGC WKT(Well-Known Text) 로 반환한다.
- maxDecimalDigits 값은 결과값의 소수점 자릿수이다. (기본값 : 15)

### ST_GEOMFROMTEXT

**Geometry ST_GEOMFROMTEXT(Text wkt)**
**Geometry ST_GEOMFROMTEXT(Text wkt, Integer srid)**

- WKT 로 작성된 Geometry 값을 Geometry 타입의 데이터로 반환한다.
- PostGIS 2.0.0 이전 버전에서는 비어있는 좌표를 조회하기 위해서는  ```ST_GeomFromText('GEOMETRYCOLLECTION(EMPTY)')``` 로 조회했으나
2.0.0 버전부터는 ```ST_GeomFromText('GEOMETRYCOLLECTION EMPTY')``` 메서드를 사용해야 한다.


## Reference

https://postgis.net/docs/