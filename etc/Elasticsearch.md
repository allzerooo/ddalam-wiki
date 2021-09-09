# Elasticsearch

Elasticsearch는 DB와 같은 테이블을 생성하면서 추가적으로 단어 단위로 짤라 해당 단어가 어떤 문서에 위치하는지를 기록하는 테이블도 생성한다. 이런 방법을 역 색인(Inverted Index)라고 한다.

이렇게 데이터를 구성하면 단어가 포함된 문서를 빠르게 찾을 수 있다. 

또한, 특정 단어군을 같은 단어로 검색되도록 처리하려면 형태소 분석 과정이 필요한데 한글 데이터는 '노리'라는 플러그인을 사용하면 된다.

<br/>

### Elasticsearch vs RDB
|Elasticsearch|RDB|
|:---:|:---:|
|Index|Database|
|Type|Table|
|Document|Row|
|Field|Column|
|Mapping|Schema|

<br/>

Elasticsearch는 REST API로 데이터 CRUD를 한다.

|Elasticsearch|RDB|
|:---:|:---:|
|GET|SELECT|
|PUT|UPDATE|
|POST|INSERT|
|DELETE|DELETE|

<br/>

### Shard

하나의 문서를 여러 개로 쪼개서 저장하는 것을 뜻한다. 쪼개진 각각의 Shard를 서로 다른 머신에 올려서 관리할 수 있는데 그러면 하나의 문서로 합쳐져 있을 때 보다 더 빠른 검색이 가능해진다. 왜냐하면, 머신의 성능은 CPU, 메모리, 디스크의 성능에 영향을 받는데 나누어 관리하게 되면 하나로 합쳐져 있을 때 보다 더 많은 성능을 사용할 수 있기 때문이다. 또한, Shard를 쉽게 스케일 아웃할 수 있으며 머신도 쉽게 스케일 아웃할 수 있다. Shard는 Elasticsearch만의 고유 기능은 아니며 RDB에도 Shard를 적용할 수 있다.

<br/>

### Replica

Replica는 복사본을 의미한다. 특정 Shard와 동일한 데이터를 저장하는 복사본으로, Replica를 사용하면 특정 머신에 문제가 생겼을 때도 서비스를 안정적으로 유지할 수 있다.

<br/>

## Elasticsearch와 DB의 차이점
1. Elasticsearch는 실시간 처리가 불가능하다 → DB는 insert를 하고 바로 조회가 되지만, Elasticsearch는 내부적으로 데이터가 처리되는 과정에서 insert를 하고 조회하기까지 잠깐의 딜레이가 필요하다
2. Elasticsearch는 트랜잭션과 롤백을 제공하지 않는다
3. Elasticsearch는 데이터를 진짜 업데이트하지 않는다.




인덱스 용량 제한 → 인덱스에 도큐먼트가 많아지면 성능이 나빠짐 → 특정 도큐먼트 개수에 도달하거나 특정 용량을 넘어서면 인덱스를 분리 or 일/주/월/년 단위 같은 날짜/시간 단위로 인덱스를 분리하기도 한다 → 데이터를 관리 목적으로 그룹핑하는 것

엘라스틱서치 도큐먼트 수정 작업은 비용이 많이 들기 때문에 권장하지 않는다. 개별 도큐먼트 수정이 많은 작업이라면 엘라스틱서치가 아닌 다른 데이터베이스를 이용하는 것이 좋다

수정과 마찬가지로 개별 도큐먼트 삭제 또한 비용이 많이 들어가는 작업이다
Elasticsearch REST APIs
Elasticsearch REST APIs

_cat
엘라스틱서치의 현재 상태를 확인할 수 있다

cat API가 지원하는 목록 확인
Kibana 콘솔

GET _cat
클러스터 내부 인덱스 목록
Kibana 콘솔

GET _cat/indices?v
CLI

curl -X GET "localhost:9200/_cat/indices?v"
_bulk
한번의 요청으로 여러 도큐먼트를 생성/수정/삭제

_mapping
인덱스 매핑값을 확인할 수 있다

Kibana 콘솔


GET index2/_mapping
 

인덱스 생성/확인/삭제
Kibana 콘솔

// 인덱스 생성 - PUT or POST 사용
PUT index1

// 인덱스 확인
GET index1

// 인덱스 삭제
DELETE index1
 

인덱싱
도큐먼트를 인덱스에 포함시키는 것

도큐먼트 생성/읽기/수정/삭제
도큐먼트 생성
Kibana 콘솔

// 인덱스와 함께 도큐먼트 생성
PUT index2/_doc/1
{
  "name": "mike",
  "age": 25,
  "gender": "male" 
}

// 새로운 필드가 추가된 도큐먼트를 인덱싱
PUT index2/_doc/2
{
  "name": "jane",
  "country": "france"
}
도큐먼트 읽기
방법
도큐먼트 아이디를 이용해 조회

쿼리 DSL (엘라스틱서치가 제공하는 쿼리문)을 이용해 조회

Kibana 콘솔

// 도큐먼트 아이디를 이용해 조회
GET index2/_doc/1

// search라는 DSL 쿼리를 이용해 조회
GET index2/_search
도큐먼트 수정
방법
도큐먼트 아이디를 이용해 수정

_update API를 이용해 수정

Kibana 콘솔

// 도큐먼트 아이디를 이용해 수정 - 도큐먼트를 인덱싱하는 과정에서 같은 도큐먼트 아이디가 있으면 덮어쓰기
PUT index2/_doc/1
{
  "name": "park",
  "age": 45,
  "gender": "male"
}

// _update API를 이용해 수정
PUT index2/_doc/1
{
  "name": "park",
  "age": 45,
  "gender": "male"
}
도큐먼트 삭제
Kibana 콘솔

DELETE index2/_doc/2
 

매핑
관계형 데이터베이스의 스키마와 비슷한 역할을 하는 것. 엘라스틱서치가 검색 엔진으로 전문 검색과 대용량 데이터를 빠르게 실시간 검색할 수 있는 이유는 매핑이 있기 때문.

다이나믹 매핑
개발자가 데이터 타입을 지정하지 않아도 엘라스틱서치가 도큐먼트의 필드와 값을 보고 JSON 도큐먼트의 데이터 타입에 맞춰 자동으로 필드의 타입이 지정되는 것

다이나믹 매핑은 인덱스의 규모가 커지면 효율이 떨어진다. 데이터의 목적에 따라 메모리를 조금 쓰는 short 타입이 유리할 수 있으며, 전문 검색보다는 집계나 정렬, 필터링을 위해 사용되는 데이터는 텍스트 타입보다는 키워드 타입으로 지정하는 것이 좋다

명시적 매핑
개발자가 데이터 타입을 직접 지정

방법
인덱스를 생성할 때 mappings의 정의를 설정

mapping API를 이용해 설정


// 인덱스를 생성할 때 mappings의 정의를 설정
PUT index3
{
  "mappings": {
    "properties": {
      "age": {"type": "short"},
      "name": {"type": "text"},
      "gender": {"type": "keyword"}
    }
  }
}
 

💡저장할 데이터를 확실히 알고 있으면 인덱스를 생성할 때 직접 매핑하는 것이 좋다. 인덱스 매핑이 정해지면 새로운 필드를 추가할 수는 있으나, 이미 정의된 필드를 수정하거나 삭제할 수는 없다. 필드 이름을 변경하거나 데이터 타입을 변경하기 위해서는 새로운 인덱스를 만들거나 reindex API를 이용해야 하니 매핑 작업은 신중하게 하는 것이 좋다.

 