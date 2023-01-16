# mybatis 에서 resultType 이 HashMap<String, Object> 인 경우 Boolean 매핑 오류

> 실무에서 실제로 있었던 이슈였다.

### 상황

스키마와 테이블 명, 조회할 컬럼 명을 파라미터로 받아 테이블의 컬럼에 맞는 데이터를 리턴해주는 다음과 같은 쿼리를 만들었다.

```xml
<select id="selectList" parameterType="SomeParameterEntity" resultType="java.util.HashMap" >
    select
        <foreach collection="columnNameList" item="item" index="index" separator=",">
            <choose>
                <!-- 소수점 일곱째 자리 까지 조회 -->
                <when test='item == "geom"'>
                      ST_AsGeoJSON(geom, 7)::json ->> 'type' as "geometry_type"
                    , ST_AsGeoJSON(geom, 7)::json ->> 'coordinates' as "coordinates"
                </when>
                <otherwise>
                    "${item}" as "${item}"
                </otherwise>
            </choose>
        </foreach>
    from ${schema}."${tableName}"
</select>
```

위에 나온 것처럼 리턴 타입은 ```resultType="java.util.HashMap``` 이었고, 
java 에서는 리턴 타입을 ```List<HashMap<String, Object>>``` 으로 작성했다.

작성하고 보니 String, Integer, Long, Double 등과 같은 데이터 타입은 정상적으로 값이 매핑되는데 **Boolean** 만 null 로 매핑되는 현상이 확인되었다.

### 원인

```DefaultResultSetHandler.java```


### 해결 방안

프로젝트 구조마다 해결 방법이 조금 다를 수 있겠지만, **TypeHandler 파일을 만들고 mybatis 설정 파일에 작성한 TypeHandler.java 파일을 매핑시켜주면 된다.**



https://mybatis.org/mybatis-3/ko/configuration.html