# PostGIS 의 공간(Spatial) 메서드 모음

PostGIS 에서 제공하는 공간 메서드를 사용하기 위해서는 기본적으로 데이터 타입이 Geometry 인 컬럼이 있어야 한다.
아래 예시들의 파라미터 타입 혹은 결과 타입이 Geometry 인 것들이 데이터 타입이 Geometry 인 컬럼이다.

### ST_CENTROID

**Geometry ST_Centroid(Geometry geometry)**

- Geometry 의 중심 좌표를 조회한다.

### ST_POINTONSURFACE

**Geometry ST_PointOnSurface(Geometry geometry)**

- Geometry 의 중심 좌표를 조회한다.


>
> **ST_CENTROID VS ST_POINTONSURFACE**
> 
> 두 함수 모두 Geometry 의 중심 좌표를 구하는 함수이지만,
> ST_POINTONSURFACE 는 실제 폴리곤 면적의 중심값을 구하는 함수이고, 
> ST_CENTROID 는 END-POINT 들의 중심점을 구하는 함수이다. 
> 
> ![ST_PointOnSurface](https://user-images.githubusercontent.com/49870384/211461148-a2bf9d67-9519-4f66-a8e9-4759b4217f41.png)
>
> **Reference**
> 
> [ST_PointOnSurface - H2](http://www.h2gis.org/docs/dev/ST_PointOnSurface/)


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

### ST_TRANSFORM

**Geometry ST_TRANSFORM(Geometry geometry, Integer srid)**
**Geometry ST_TRANSFORM(Geometry geometry, Text proj)**
**Geometry ST_TRANSFORM(Geometry geometry, Text from_proj, Text to_proj)**
**Geometry ST_TRANSFORM(Geometry geometry, Text from_proj, Integer to_srid)**

- Geometry 값의 좌표를 변환한다.
- ```from_proj``` 파라미터가 없는 경우에는 Geometry 값에 SRID 가 정의되어 있어야 한다.

### ST_SETSRID

**Geometry ST_SETSRID(Geometry geometry, Integer srid)**

- 이미 정의되어 있거나, 정의되지 않은 Geometry 값에 SRID 값을 설정한다.
- 단순히 SRID 값만 설정할 뿐, 실제 좌표값에는 변화가 없다.

### ST_ASGEOJSON

**Text(JSON) ST_ASGEOJSON(Geometry Geometry, Integer maxdecimaldigits)**   
**Text(JSON) ST_ASGEOJSON(Geometry Geometry, Integer maxdecimaldigits, Integer Options)**

- Geometry 정보를 GeoJson 형태로 반환한다.
- 2D, 3D 모두 지원가능하다.
- maxdecimaldigits 은 소수점 자리 수를 지정한다. (max : 9)
- 다음은 Options 에 들어갈 수 있는 정보이다.
  - 0 : 옵션을 설정하지 않는다
  - 1 : GeoJson BBOX 도 함께 리턴한다.
  - 2 : CRS 약어도 함께 리턴한다 ex) EPSG:4326
  - 4 : CRS 의 정식 명칭도 함께 리턴한다 ex) urn:ogc:def:crs:EPSG::4326
  - 8 : EPSG 가 아닌 경우 CRS:4326 등의 값으로 리턴한다.


### ST_EstimatedExtent

**box2d ST_EstimatedExtent(text schema_name, text table_name, text geocolumn_name, boolean parent_only)**
**box2d ST_EstimatedExtent(text schema_name, text table_name, text geocolumn_name)**
**box2d ST_EstimatedExtent(text table_name, text geocolumn_name)**

- 공간 테이블의 예상 범위를 box2d 형태로 저장한다.
  - box2d : BOX(xmin, ymin, xmax, ymax) 형태의 오브젝트
- ST_EXTENT 함수보다 속도적인 측면에서 훨씬 유리하다.
- 지오서버에서 BBOX 를 조회하는 데 사용된다.

```
ST_EstimatedExtent 결과가 null 이 나오는 경우

데이터가 없는 경우
ST_EstimatedExtent 함수는 특정 테이블 또는 뷰의 공간 데이터의 경계 상자를 추정합니다. 하지만 데이터가 전혀 없는 경우에는 결과가 NULL이 됩니다.

공간 데이터의 SRID가 설정되어 있지 않은 경우
ST_EstimatedExtent 함수는 공간 데이터의 SRID (Spatial Reference ID)를 기반으로 경계 상자를 계산합니다. SRID가 설정되어 있지 않은 경우 함수가 NULL을 반환합니다. SRID를 설정하려면 ALTER TABLE 구문을 사용하여 공간 컬럼에 SRID를 할당해야 합니다.

인덱스가 생성되어 있지 않은 경우
ST_EstimatedExtent 함수는 인덱스를 사용하여 경계 상자를 계산합니다. 따라서 인덱스가 생성되어 있지 않은 경우에는 함수가 NULL을 반환할 수 있습니다. 인덱스를 생성하려면 CREATE INDEX 구문을 사용하여 공간 인덱스를 생성해야 합니다.

데이터의 크기가 너무 작은 경우
ST_EstimatedExtent 함수는 경계 상자를 계산하기 위해 공간 데이터의 크기를 기반으로 합니다. 데이터의 크기가 너무 작은 경우에는 결과가 부정확하거나 NULL이 될 수 있습니다.

공간 데이터에 오류가 있는 경우
ST_EstimatedExtent 함수는 공간 데이터에 대해 오류를 검증하지 않습니다. 따라서 공간 데이터에 오류가 있는 경우에는 함수가 NULL을 반환할 수 있습니다.
```


> ###  ST_EstimatedExtent + 좌표변환
> 
> 다음은 ST_EstimatedExtent 함수로 조회한 BBOX 영역의 좌표를 변환하는 예제 쿼리이다.
> 
> ```sql
> WITH bbox AS (   
>   SELECT public.ST_Transform(public.st_setsrid(public.ST_EstimatedExtent(#{스키마명}, #{테이블명}, #{좌표컬럼명}), #{테이블에정의된좌표계}::int),#{변환할좌표계}::int) AS bbox   
> )   
> SELECT   
>   public.ST_Xmin(bbox.bbox) AS xmin,   
>   public.ST_Ymin(bbox.bbox) AS ymin,   
>   public.ST_Xmax(bbox.bbox) AS xmax,   
>   public.ST_Ymax(bbox.bbox) AS ymax   
> FROM bbox;
> ``` 


### ST_EXTENT


## Reference

https://postgis.net/docs/