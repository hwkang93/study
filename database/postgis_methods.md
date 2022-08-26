


### ST_CENTROID

**Geometry ST_Centroid(Geometry)**

- Geometry 의 중심 좌표를 조회한다.

### ST_X & ST_Y

**Float ST_X(Point_Geometry)** & **Float ST_Y(Point_Geometry)**

- Geometry 의 X 좌표(또는 Y 좌표)를 조회한다.
- Geometry 중 Point 데이터에서만 메소드를 사용할 수 있다.

------------------------------------

### ST_SRID

**Integer ST_SRID(Geometry)**

- Geometry 의 좌표계를 조회한다.

```sql
SELECT ST_SRID(ST_GeomFromText('POINT(-71.1043 42.315)',4326));

-- result : 4326
```



## Reference

https://postgis.net/docs/