# PostgreSQL GeoJson (feat. PostGIS)

## ST_AsGeoJson

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

## 예제

예제만으로 간단하고 원활하게 테스트할 수 있도록 CTE(common table expression) 을 예제 테이블로 사용하겠다.

```sql
with temp_geom as (
    select '1' as FID, st_geomfromtext('POINT (192648.6377 558235.7251)', 5186) as GEOM union all
    select '2' as FID, st_geomfromtext('POINT (191424.9673 553172.0472)', 5186) as GEOM union all
    select '3' as FID, st_geomfromtext('POINT (199791.3484 551586.6432)', 5186) as GEOM union all
    select '4' as FID, st_geomfromtext('POINT (207063.0387 553515.305)', 5186) as GEOM union all
    select '5' as FID, st_geomfromtext('POINT (201485.7022 554434.168)', 5186) as GEOM union all
    select '6' as FID, st_geomfromtext('POINT (208207.775 556368.7968)', 5186) as GEOM union all
    select '7' as FID, st_geomfromtext('POINT (198448.0479 552671.8822)', 5186) as GEOM union all
    select '8' as FID, st_geomfromtext('POINT (189203.00619999997 547586.2251)', 5186) as GEOM
)
select
	ST_AsGeoJSON(geom)::json ->> 'type' as "type" ,
	ST_AsGeoJSON(geom)::json ->> 'coordinates' as "coordinates"
from temp_geom;
```

위와 같은 Geometry 정보를 가진 테이블이 있다고 가정했을 때, ```geom``` 을 조회하면 다음과 같은 결과를 볼 수 있다. 

```sql
-- 실행 쿼리
select * from temp_geom;
```

```
-- 결과
fid|geom                                  |
---|--------------------------------------|
1  |POINT (192648.6377 558235.7251)       |
2  |POINT (191424.9673 553172.0472)       |
3  |POINT (199791.3484 551586.6432)       |
4  |POINT (207063.0387 553515.305)        |
5  |POINT (201485.7022 554434.168)        |
6  |POINT (208207.775 556368.7968)        |
7  |POINT (198448.0479 552671.8822)       |
8  |POINT (189203.00619999997 547586.2251)|
```

위의 좌표정보를 **ST_ASGEOJSON** 함수를 사용하면 GeoJson 형태로 조회할 수 있다.

```sql
-- 실행 쿼리
select
    *,
    ST_ASGEOJSON(geom) as geojson
from temp_geom;
```

```
-- 결과
fid|geom                                  |geojson                                                                                                    |
---|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
1  |POINT (192648.6377 558235.7251)       |{"type":"Point","crs":{"type":"name","properties":{"name":"EPSG:5186"}},"coordinates":[192648.6377,558235.7251]}|
2  |POINT (191424.9673 553172.0472)       |{"type":"Point","crs":{"type":"name","properties":{"name":"EPSG:5186"}},"coordinates":[191424.9673,553172.0472]}|
3  |POINT (199791.3484 551586.6432)       |{"type":"Point","crs":{"type":"name","properties":{"name":"EPSG:5186"}},"coordinates":[199791.3484,551586.6432]}|
4  |POINT (207063.0387 553515.305)        |{"type":"Point","crs":{"type":"name","properties":{"name":"EPSG:5186"}},"coordinates":[207063.0387,553515.305]} |
5  |POINT (201485.7022 554434.168)        |{"type":"Point","crs":{"type":"name","properties":{"name":"EPSG:5186"}},"coordinates":[201485.7022,554434.168]} |
6  |POINT (208207.775 556368.7968)        |{"type":"Point","crs":{"type":"name","properties":{"name":"EPSG:5186"}},"coordinates":[208207.775,556368.7968]} |
7  |POINT (198448.0479 552671.8822)       |{"type":"Point","crs":{"type":"name","properties":{"name":"EPSG:5186"}},"coordinates":[198448.0479,552671.8822]}|
8  |POINT (189203.00619999997 547586.2251)|{"type":"Point","crs":{"type":"name","properties":{"name":"EPSG:5186"}},"coordinates":[189203.0062,547586.2251]}|
```

GeoJson 도 Json 이니 PostgreSQL 의 JSON 조회 문법을 사용할 수 있다.   
예를 들어 **Geometry 타입, 좌표계, 좌표 를 각각 조회해야 하는 경우** 다음과 같은 쿼리를 작성할 수도 있다.

```sql
-- 실행 쿼리
select
    fid,
    ST_AsGeoJSON(geom)::json ->> 'type' as "type" ,
	ST_AsGeoJSON(geom)::json -> 'crs' -> 'properties' ->> 'name' as "crs",
	ST_AsGeoJSON(geom)::json ->> 'coordinates' as "coordinates"
from temp_geom;
```

```
-- 결과
fid|type |crs      |coordinates              |
---|-----|---------|-------------------------|
1  |Point|EPSG:5186|[192648.6377,558235.7251]|
2  |Point|EPSG:5186|[191424.9673,553172.0472]|
3  |Point|EPSG:5186|[199791.3484,551586.6432]|
4  |Point|EPSG:5186|[207063.0387,553515.305] |
5  |Point|EPSG:5186|[201485.7022,554434.168] |
6  |Point|EPSG:5186|[208207.775,556368.7968] |
7  |Point|EPSG:5186|[198448.0479,552671.8822]|
8  |Point|EPSG:5186|[189203.0062,547586.2251]|
```