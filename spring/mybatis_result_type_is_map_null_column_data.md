# mybatis 사용시 resultType 이 map 인 경우 값이 null 일 때 column 명을 key 로 생성하는 방법

MyBatis 로 쿼리 요청 시, ResultMap 이 java.util.Map 의 구현체인 경우 (HashMap, LinkedHashMap 등) value 값이 null 일 때, 컬럼 명이 누락되는 현상이 발생한다.

mybatis-config.xml 파일에 다음 값을 추가해주면 해결할 수 있다.

```xml
  <settings>
	  <setting name="callSettersOnNulls" value="true"/>
  </settings>
```
