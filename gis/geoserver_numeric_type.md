# GeoServer 에서 인식하는 숫자형 타입 모음

PostgreSQL 테이블 레이어 발행 시 GeoServer 에서 인식하는 **숫자형 타입**을 정리한 것이다.

| PostgreSQL       | GeoServer  |
|------------------|------------|
| int              | Integer    |
| integer          | Integer    |
| bigint           | Long       |
| smallint         | Short      |
| double precision | Double     |
| float            | Double     |
| real             | Float      |
| numeric          | BigDecimal |
