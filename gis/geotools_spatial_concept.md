# GeoTools 에서 사용하는 공간 개념 (Spatial Concept)

GeoTools 에서는 지리적 분석 또는 공간 통계적 연구에서 사용되는 다양한 공간 개념(Spatial Concept)을 나타내는 상수들을 정의한다.
상수들은 ``org.geotools.process.spatialstatistics.enumeration.SpatialConcept`` 클래스에 정의되어 있다.
이러한 공간 개념은 데이터 간의 공간적 상호작용 및 관계를 모델링하고 분석하는 데 사용된다. 각 상수의 간단한 설명은 다음과 같다.

### InverseDistance

두 지점 간의 거리에 역수를 취한 값을 사용하여 공간적 가중치를 생성한다. 가까운 지점은 높은 가중치를 갖고, 먼 지점은 낮은 가중치를 갖는다.

### InverseDistanceSquared

두 지점 간의 거리의 제곱에 역수를 취한 값을 사용하여 공간적 가중치를 생성한다. 거리의 제곱에 역수를 취하므로 가까운 지점은 더 높은 가중치를 갖는다.

### FixedDistance

고정된 거리 내의 지점들에 대한 공간적 관계를 정의한다. 거리 내에 있는 지점들은 관련성을 가지고, 거리 밖의 지점들은 무시된다.

### ZoneOfIndifference

공간적 관계가 생성되는 지점 사이의 '무관심 영역'을 나타내며, 일정 거리 내에 있는 지점들은 연결되고, 그 밖의 지점들은 무시된다.

### KNearestNeighbors

각 지점에 대해 가장 가까운 K개의 이웃을 식별하고 이러한 이웃들과의 관계를 모델링한다. K 값은 주어진 이웃의 수를 나타낸다.

### ContiguityEdgesNodes

이웃 관계가 지리적 특성의 경계와 점 모두에 대해 정의된다. 이러한 관계는 지리적 연결성을 나타내며, 엣지와 노드 사이의 관계를 모델링한다.

### ContiguityEdgesOnly

이웃 관계가 지리적 특성의 경계에만 적용된다. 이러한 관계는 지리적 경계에 따라 결정된다.

### ContiguityNodesOnly

이웃 관계가 지리적 특성의 점에만 적용됩니다. 이러한 관계는 지리적 지점 사이의 연결성을 모델링한다.

### WeightsFromFile

가중치 정보가 외부 파일에서 제공되며, 이 파일을 사용하여 공간 가중치 행렬을 생성한다. 이 방법은 사용자가 직접 가중치를 정의하고 사용할 수 있도록 한다.