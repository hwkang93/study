# mybatis 에서 resultType 이 HashMap<String, Object> 인 경우 Boolean 매핑 오류

> 실무에서 실제로 있었던 이슈였다.

### 상황

스키마와 테이블 명, 조회할 컬럼 명을 파라미터로 받아 테이블의 컬럼에 맞는 데이터를 공간정보와 함께 리턴해주는 다음과 같은 쿼리를 만들었다.

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

디버깅을 하다 보니 ```DefaultResultSetHandler.java``` 클래스에 쿼리 결과값을 ```metaObject``` 객체에 매핑해주는  ```applyAutomaticMappings``` 메서드가 있었다.

```java
private boolean applyAutomaticMappings(ResultSetWrapper rsw, ResultMap resultMap, MetaObject metaObject, String columnPrefix) throws SQLException {
    List<UnMappedColumnAutoMapping> autoMapping = createAutomaticMappings(rsw, resultMap, metaObject, columnPrefix);
    boolean foundValues = false;
    if (!autoMapping.isEmpty()) {
      for (UnMappedColumnAutoMapping mapping : autoMapping) {
        final Object value = mapping.typeHandler.getResult(rsw.getResultSet(), mapping.column);
        if (value != null) {
          foundValues = true;
        }
        if (value != null || (configuration.isCallSettersOnNulls() && !mapping.primitive)) {
          // gcode issue #377, call setter on nulls (value is not 'found')
          metaObject.setValue(mapping.property, value);
        }
      }
    }
    return foundValues;
  }
```

여기서 ResultSetWrapper 객체를 확인해보면 다음과 같은 필드들을 확인할 수 있다.

```java
public class ResultSetWrapper {
    //...
    private final List<String> columnNames = new ArrayList<>();   // 컬럼명
    private final List<String> classNames = new ArrayList<>();    // JAVA 에 매핑 될 컬럼 타입
    private final List<JdbcType> jdbcTypes = new ArrayList<>();   // JDBC 내 컬럼 타입
    
    //...
}
```

columnNames 은 컬럼명   
classNames 은 JAVA 에 매핑 될 컬럼 타입   
jdbcTypes 은 JDBC 내 컬럼 타입인데,

이 값들을 바탕으로 JDBC 타입을 JAVA 타입으로 형변환 후 값을 매핑시켜주는 작업을 진행한다.

여기서 Boolean 타입의 경우 Java 에서는 Boolean 로, JDBC 에서는 Bit 으로 판단하여 타입이 매핑이 안돼 결과 데이터가 Null 로 출력되었다.


### 해결 방안

프로젝트 구조마다 해결 방법이 조금 다를 수 있겠지만, **BooleanTypeHandler 파일을 만들고 mybatis 설정 파일에 작성한 BooleanTypeHandler.java 파일을 매핑시켜줬다.**

```java
import lombok.extern.slf4j.Slf4j;
import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;
import org.apache.ibatis.type.MappedJdbcTypes;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

@MappedJdbcTypes(JdbcType.BIT)
@Slf4j
public class BooleanTypeHandler extends BaseTypeHandler<Boolean> {

    //org.apache.ibatis.type.BooleanTypeHandler 의 setNonNullParameter 메소드와 같음
    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, Boolean parameter, JdbcType jdbcType) throws SQLException {
        ps.setBoolean(i, parameter);
    }

    //null 일 경우 null 로 리턴한다.
    @Override
    public Boolean getNullableResult(ResultSet rs, String columnName) throws SQLException {
        boolean result = rs.getBoolean(columnName);

        return rs.wasNull() ? null : result;
    }

    @Override
    public Boolean getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        boolean result = rs.getBoolean(columnIndex);
        return rs.wasNull() ? null : result;
    }

    @Override
    public Boolean getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        return cs.wasNull() ? null : cs.getBoolean(columnIndex);
    }
}
```

그리고 작성한 클래스를 ```mybatis-config.xml``` 파일에 매핑시켜줬다.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "./dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true" />
        <setting name="callSettersOnNulls" value="true"/>
    </settings>
    <typeHandlers>
        <typeHandler handler="../../...BooleanTypeHandler"/>
    </typeHandlers>
</configuration>
```

## Reference

https://mybatis.org/mybatis-3/ko/configuration.html