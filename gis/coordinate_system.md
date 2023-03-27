# 좌표계

좌표계(Coordinate System)는 지구의 표면상의 지점을 특정 방법으로 표현하는 데 사용되는 기준 체계입니다. 
지구는 3차원적인 형태이지만, 지도나 공간 데이터를 분석하고 표현하기 위해서는 이를 2차원적인 좌표로 표현해야 합니다. 
따라서 좌표계는 지구의 3차원 공간을 2차원 좌표 평면에 투영(projection)하여 표현하는 방법을 정의합니다.

좌표계에는 크게 두 가지 유형이 있다.

### 지리적 좌표계(Geographic Coordinate System)

- 지구의 공간을 위도(Longitude)와 위도(Latitude)와 같은 각도로 표현한다.
- 각도의 형태로 표현되므로 주로 구면 좌표계로 분류된다.
- 경위도(WGS84, EPSG:4326)는 가장 널리 사용되는 지리적 좌표계 중 하나이다.

### 투영 좌표계(Projected Coordinate System)

- 지구의 3차원 공간을 2차원 좌표로 투영하여 표현합니다.
- 주로 면적, 거리, 각도 등의 특정 속성을 보다 정확하게 표현하기 위해 사용됩니다.
- UTM(Universal Transverse Mercator), Lambert Conformal Conic 등이 흔히 사용되는 투영 좌표계입니다.

좌표계는 다양한 목적과 지역에 따라 다양하게 적용됩니다. 
지도, GIS 분석, GPS 위치 기반 서비스 등에서는 올바른 좌표계의 선택이 중요합니다. 
좌표계를 사용하여 공간 데이터를 표현하고 변환할 때는 서로 다른 좌표계 간의 변환을 수행할 수 있는 방법이 필요합니다. 
이를 위해 좌표계 변환 알고리즘과 라이브러리가 활용됩니다.

## SRID (좌표계 ID)

SRID 는 공간 데이터베이스에서 사용되는 Spatial Reference Identifier 의 약자입니다. 
SRID 는 공간 데이터의 좌표계를 식별하는 고유한 숫자 코드로서, 해당 데이터가 어떤 좌표계를 사용하는지를 명시적으로 지정합니다.

여러 가지 지리 정보 시스템(GIS)이나 공간 데이터베이스에서는 다양한 좌표계를 지원합니다. 
이때 SRID 를 사용하여 각 데이터가 어떤 좌표계를 사용하는지를 명시하고, 데이터 간의 정확한 위치 및 공간 분석을 위해 필요한 좌표계 변환을 수행할 수 있습니다.

예를 들어, WGS84 좌표계를 사용하는 데이터는 SRID 4326을 갖고 있고, Universal Transverse Mercator (UTM) 좌표계를 사용하는 데이터는 해당 UTM 존에 따라 SRID 가 할당됩니다.

SRID 는 각 지역의 공간 데이터베이스에서 사용하는 좌표계를 일관성 있게 관리하고, 데이터 간의 상호 호환성을 유지하기 위해 중요한 역할을 합니다. 
데이터를 쿼리하거나 분석할 때 올바른 SRID 를 지정하는 것이 중요하며, 이를 통해 정확한 공간 정보를 얻을 수 있습니다.


## Reference

ChatGPT