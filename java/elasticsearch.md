# ElasticSearch

Elasticsearch 는 Apache Lucene(아파치 루씬) 기반의 Java 오픈소스 분산 검색 엔진이다.
Elasticsearch 는 방대한 양의 데이터를 신속하게, 거의 실시간(NRT, Near Real Time) 으로 저장, 검색, 분석할 수 있다.

Elasticsearch 는 검색을 위해 단독으로 사용되기도 하고 **ELK(Elasticsearch, Logstash, Kibana) 스택** 으로 사용되기도 한다.

## ELK Stack

- **Logstash**
    다양한 소스(DB, csv 파일 등)의 로그 또는 트랜잭션 데이터를 수집, 집계, 파싱하여 Elasticsearch 로 전달

- **Elasticsearch**
    Logstash 로 부터 받은 데이터를 검색 및 집계를 하여 필요한 관심 있는 정보를 획득

- **Kibana**
    Elasticsearch 의 빠른 검색을 통해 데이터를 시각화 및 모니터링

## Elasticsearch Architecture

<img src="https://raw.githubusercontent.com/exo-addons/exo-es-search/master/doc/images/image_05.png">

(출처 : https://github.com/exo-archives/exo-es-search)

<br/>

클러스터 > 노드 > 인덱스 > 샤드(Lucene Index) > Index Segment(Inverted index)

인덱스는 기본적으로 1초마다 새로고침된다.

노드 간에 동기화되지 않아 검색자가 이력을 간략하게 조회할 수 있다.

-> Elasticsearch 는 **거의 실시간** 이지 실제로 실시간은 아니다.

### 클러스터 (Cluster)

클러스터란 Elasticsearch 에서 가장 큰 시스템 단위를 의미하며, 최소 하나 이상의 노드들로 이루어진 노드들의 집합이다.
서로 다른 클러스터는 데이터의 접근, 교환을 할 수 없는 독립적인 시스템으로 유지되며,
여러 대의 서버가 하나의 클러스터를 구성할 수 있고, 한 서버에 여러 개의 클러스터가 존재할 수도 있다.


### 노드 (Node)

Elasticsearch 를 구성하는 하나의 단위 프로세스를 의미한다.
노드는 역할에 따라 Master-eligible, Data, Ingest, Tribe 노드로 구분할 수 있다.

#### Master-eligible node

클러스터를 제어하는 마스터로 선택할 수 있는 노드를 말한다.

**마스터 노드의 역할**
- 인덱스 생성, 삭제
- 클러스터 노드들의 추적, 관리
- 데이터 입력 시 어느 샤드에 할당할 것인지 결정

#### Data node

데이터와 관련된 CRUD 작업과 관련있는 노드이다.
CPU, 메모리 등 자원을 많이 소모하므로 모니터링이 필요하고, master 노드와 분리되는 것이 좋다.

#### Ingest node

데이터를 변환하는 등 사전 처리 파이프라인을 실행하는 역할을 한다.

#### Coordination only node

data noe 와 master-eligible node 의 일을 대신하는 이 노드는 대규모 클러스터에서 이점이 있다.
로드벨런서와 비슷한 역할을 한다.


### 인덱스 (Index)

Elasticsearch 에서 index 는 RDBMS 에서 database 와 대응하는 개념이다.
또한 샤드(shard)와 복제(replica) 는 Elasticsearch 에만 존재하는 개념이 아니라, 분산 데이터베이스 시스템에도 존재하는 개념이다.


### 샤드 (Shard)

Sharding 은 데이터를 분산해서 저장하는 방법을 의미한다.
즉 Elasticsearch 에서 스케일 아웃을 위해 index 를 여러 shard 로 쪼갠 것이다.
기본적으로 1개 존재하며, 검색 성능 향상을 위해 클러스터의 샤드 개수를 조정하는 튜닝을 하기도 한다.


### 복제 (Replica)

Replica 는 또다른 형태의 Shard 라고 할 수 있다.
노드를 손실했을 경우 데이터의 신뢰성을 위해 샤드들을 복제하는 것이다.
따라서 replica 는 서로 다른 노드에 존재할 것을 권장한다.



## Elasticsearch 의 특징

- **스케일 아웃(Scale out)**
    샤드를 통해 규모가 수평적으로 늘어날 수 있다.
- **고가용성**
    복제를 통해 데이터의 안정성을 보장한다.
- **Schema Free**
    Json 문서를 통해 데이터 검색을 수행하므로 스키마 개념이 없다.
- **Restful**
    데이터 CRUD 작업은 HTTP Restful API 를 통해 수행하며 다음과 같이 대응한다.

    |Data CRUD|Elasticsearch Restful| 
    |---|---|
    |SELECT|GET|
    |INSERT|PUT|
    |UPDATE|POST|
    |DELETE|DELETE|

> INSERT - PUT / UPDATE - POST 맞아?



## 역색인 (Inverted index)

관계형 데이터베이스(Relational Database Management System 이하 RDBMS) 에서는 데이터를 테이블 형태로 저장한다.
그리고 관계형 데이터베이스는 방대한 양의 데이터를 원활히 조회하기 위해 테이블의 열 기준으로 인덱스를 만들어 데이터를 조회하며,
여기서 인덱스는 책의 맨 앞에 있는 목차에 비유할 수 있다.

**이와 반대로 Elasticsearch 는 inverted index 라는 구조로 저장한다.**
텍스트를 다 뜯어 검색어 사전을 만들고 검색어 사전을 이용해 검색을 진행한다.
여기서 검색어 사전은 책의 맨 뒤에 있는 색인과 같다.

> **RDBMS 와 Elasticsearch 비교**
> 
> |                          |   RDBMS   |   Elasticsearch |
> | :----:                   |   :---:   |       :---:     | 
> |데이터 저장 방식             |   정규화   |     역정규화     |  
> |전문(Full Text) 검색 속도   |   느림     |       빠름       |
> |의미 검색                  |   불가능    |       가능      | 
> |Join                      |   가능     |      불가능      | 
> |수정,삭제                  |      빠름  |       느림       |


### 텍스트 처리 과정
   1. 단어의 대소문자를 변환한다.(대문자 -> 소문자) 
   2. 토큰(단어)을 재정렬한다.(보통 ascii 순서로 정렬한다고 한다.)
   3. 불용어(stopwords, 검색어로서의 가치가 없는 단어 ex) a, an, are, be, but ...)를 제거한다.
   4. 형태소 분석 작업을 한다. (보통 ~s, ~ing 등을 제거하는 작업이며, 한글의 경우 의미 분석을 해야 해서 더 복잡하다고 한다.)
   5. 형태소 분석 작업 결과물을 토큰과 병합한다. 
      (형태소 분석을 통해 jumping, jumps -> jump 로 변환됐다면 기존의 jump 토큰과 병합한다.)
   6. 동의어를 처리한다.(fast = quick)


## Elasticsearch 사용 시 주의사항?

- 로그는 가능하면 날짜별로 나눠서 작성해야 한다.
- 원본 데이터는 항상 잘 가지고 있어야 한다. 새로 부어야 하는 경우가 많다.
- 세그먼트 병합은 사용하지 않는 시간에 하자.

# Reference

[victolee blog - [Elasticsearch] 기본 개념 잡기](https://victorydntmd.tistory.com/308)

[exo-es-search](https://github.com/exo-archives/exo-es-search)

[검색엔진이 데이터를 다루는 법 - 김종민](https://www.slideshare.net/kjmorc/ss-80803233)