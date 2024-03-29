- [Elasticsearch: 분산 검색 엔진](#elasticsearch-분산-검색-엔진)
    - [Elasticsearch vs RDB](#elasticsearch-vs-rdb)
    - [Shard](#shard)
    - [Replica](#replica)
  - [Elasticsearch와 DB의 차이점](#elasticsearch와-db의-차이점)
  - [매핑](#매핑)
    - [멀티 필드](#멀티-필드)
  - [Runtime fields](#runtime-fields)
    - [주소 분석기](#주소-분석기)
- [검색](#검색)
  - [쿼리 컨텍스트](#쿼리-컨텍스트)
    - [유사도 스코어](#유사도-스코어)
      - [BM25 알고리즘](#bm25-알고리즘)
        - [IDF 계산](#idf-계산)
        - [TF 계산](#tf-계산)
  - [필터 컨텍스트](#필터-컨텍스트)
  - [쿼리 사용 방법](#쿼리-사용-방법)
    - [쿼리 스트링](#쿼리-스트링)
    - [Query DSL](#query-dsl)
  - [Query DSL](#query-dsl-1)
    - [리프 쿼리](#리프-쿼리)
      - [`full text` 쿼리](#full-text-쿼리)
      - [`term level` 쿼리](#term-level-쿼리)
      - [Wildcard 쿼리](#wildcard-쿼리)
    - [복합 쿼리](#복합-쿼리)
      - [작성 포맷](#작성-포맷)
    - [지원하는 타입](#지원하는-타입)
  - [부스팅 기법 : 필드에 가중치 두기](#부스팅-기법--필드에-가중치-두기)
  - [범위 쿼리](#범위-쿼리)
- [집계(aggregation)](#집계aggregation)
  - [집계 요청 기본 형태](#집계-요청-기본-형태)
  - [메트릭 집계](#메트릭-집계)
    - [Cardinality](#cardinality)
      - [HyperLogLog++ 알고리즘](#hyperloglog-알고리즘)
  - [버킷 집계](#버킷-집계)
- [Script](#script)


# Elasticsearch: 분산 검색 엔진

엘라스틱서치는 검색 엔진이지만 구글, 네이버 같은 포털 서비스와는 다르다. 검색 엔진은 내부적으로 각 도큐먼트를 인덱싱하고 빠르게 검색하는데 사용하는 기술로 엘라스틱서치 검색 엔진을 이용해 상위에서 구글, 네이버 같은 서비스를 만들게 된다. 엘라스틱서치는 모든 레코드를 JSON 도큐먼트 형태로 입력하고 관리한다. 또한 텍스트 외에도 숫자, 날짜, IP 주소, 지리 정보 등 다양한 데이터 타입에 대해 최적화되어 있다. 검색 엔진이면서 데이터베이스이기도 한 일종의 NoSQL 데이터베이스라고 생각할 수 있다.

엘라스틱서치는 텍스트나 도큐먼트의 경우 인덱싱 시점에 분석을 거쳐 용어 단위로 분해되고 역인덱스 사전을 구축한다. 숫자나 키워드 타입의 데이터들은 집계에 최적화된 컬럼 기반 자료구조를 저장한다. 엘라시틱서치는 이렇게 최적화된 자료구조들을 바탕으로 병렬 처리나 분산 처리를 할 수 있다. 엘라스틱서치가 다른 제품이 상상하기 어려운 빠른 검색과 집계 성능을 실현하는 이유는 검색 엔진인 동시에 데이터베이스이기 때문이다. 이론적으로는 충분한 크기의 엘라스틱서치 클러스터가 구성되어 있다면 데이터의 양과 무관하게 1초 이내의 응답 속도를 기대할 수 있다. 이처럼 다른 제품을 압도하는 검색 기능과 성능이 엘라스틱서치의 가장 큰 특징이자 활용 목적이라 볼 수 있다.

검색 엔진으로서 엘라스틱서치의 중요한 특징 중 하나는 스코어링(scoring), 즉 연과도에 따른 정렬이다. 단순히 필드값을 기준으로 한 정렬은 어떤 데이터베이스에서도 제공되지만, 엘라스틱사치는 검색어에 대한 유사도 스코어를 기반으로 한 정렬을 제공한다. 이는 특히 복잡한 문자열 콘텐츠에서 검색을 수행할 때 큰 효과를 보인다. 이 외에도 다양한 스코어링 방법을 포함해 사용자가 정렬 방식을 다양하게 정의할 수 있다는 특성은 용도에 따라 큰 장점으로 작용한다.

분산 시스템으로서 엘라스틱서치는 복수의 인스턴스를 병렬로 배치하고 분산 처리해 검색 속도를 무한히 확장할 수 있게 한다. 또한 노드 간 복제 기능을 통해 일부 노드가 다운되더라도 정상적으로 서비스를 지속할 수 있게 했다. 무엇보다 편리한 점은 모든 통신을 REST API를 이용하도록 만들어 프로그래밍 언어와 무관하게 사용자가 쉽게 접근할 수 있도록 활용성을 높였다는 것이다.

물론 엘라스틱서치에도 단점은 있다. 저장공간이 크게 압축되지 않고 시스템 리소스를 많이 사용한다. 엘라스틱서치는 DSL(Domain Specific Language) 쿼리를 채용하는데 JSON 쿼리가 사실상 어렵기 때문에 반정규화를 기본으로 모델링해야 한다. 또한 인덱스가 불변의 자료구조이기 때문에 도큐먼트를 수정하거나 삭제할 경우에 비용이 저렴하지 않다. 하지만 이러한 단점들은 검색 성능을 끌어오리기 위해 어느 정도 트레이드오프가 이뤄진 것으로, 엘라스틱서치가 필요한 수준의 대량 데이터를 처리할 때는 일반적으로 용인되는 제약들이다. 결국 엘라스틱서치는 대용량 데이터에 대한 빠른 검색과 집계가 필요할 경우 우선적으로 고려해야 하는 데이터베이스 중 하나라고 할 수 있다.

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

<br/>

---

<br/>

## 매핑

### 멀티 필드
단일 필드 입력에 대해 여러 하위 필드를 정의하는 기능으로, `fields`라는 매핑 파라미터가 사용된다. `fields`는 하나의 필드를 여러 용도로 사용할 수 있게 만들어준다.

예를 들어, 문자열의 경우 전문 검색과 키워드 검색이 모두 필요한 경우가 있다.

```
...
  "contents": {
    "type": "text",
    "fields": {
      "keyword": {"type": "keyword"}
    }
  }
...
```

<br/>

## Runtime fields
쿼리 시 평가되는 필드
- 데이터를 다시 인덱싱하지 않고 기존 문서에 필드를 추가할 수 있다
- 데이터가 어떻게 구성되어 있는지 모르더라도 데이터 작업을 시작할 수 있다
- 쿼리 시 인덱싱된 필드에서 반환된 값을 재정의
- 기본 스키마를 수정하지 않고 특정 용도를 위한 필드를 정의

<br/>

### 주소 분석기

"충북"으로 검색했을 때 "충청북도", "충북" 이 포함된 도큐먼트가 검색되고, "충청북도"로 검색했을 때도 "충청북도", "충북"이 포함된 도큐먼트가 검색되도록 하는 분석기이다.

```
PUT order_address_index 
{
  "mappings" : {
    "properties": {
      "delivery_address1": {
        "type": "text",
        "fields": {
          "analyzed": {
            "type": "text",
            "analyzer": "nori_address_analyzer"
          }
        }
      }
    }
  },
  "settings": {
    "analysis": {
      "analyzer": {
        "nori_address_analyzer": {
          "tokenizer": "nori_mixed",
          "filter": [
            "length_filter", "synonym_filter", "unique_filter"
          ]
        }
      }, 
      "tokenizer": {
        "nori_mixed": {
          "type": "nori_tokenizer",
          "decompound_mode": "discard"
        }
      },
      "filter": {
        "length_filter": {
          "type": "length",
          "min": 2
        },
        "synonym_filter": {
          "type": "synonym_graph",
          "synonyms": ["충청남도, 충남", "충청북도, 충북", "경상북도, 경북", "경상남도, 경남", "전라남도, 전남", "전라북도, 전북"]
        },
        "unique_filter": {
          "type": "unique"
        }
      }
    }
  }
}
```

- 한글 분석기 `nori`를 사용
  - Elasticsearch 버전마다 플러그인의 사용법이 달라질 수 있으니 → elasticsearch VERSION nori 로 검색해서 공식 문서를 참고하는게 좋다
- `synonym` 필터를 사용해서 동의어 처리
- `unique` 필터를 사용해서 중복된 결과 제거

<br/>

---

# 검색

과거 엘라스틱서치의 검색은 크게 쿼리 컨텍스트와 필터 컨텍스트로 구분된다. 둘을 구분하는 특별한 API가 있는 것은 아니며 모두 search API를 사용한다. 본문에 들어가는 내용에 따라 쿼리가 구분된다.

## 쿼리 컨텍스트
- 유사도를 계산해 더 정확한 결과를 먼저 보여준다
- '포함', '최대한 비슷'
- 유사도 스코어 결과를 제공 : `_score`

<br/>

```
GET ecommerce/_search
{
  "query": {
    "match": {
      "category": "clothing"
    }
  }
}
```
→ category 필드의 역인덱스 테이블에 'clothing' 용어가 있는 도큐먼트를 찾아달라는 요청
- `match`: 전문 검색을 위한 쿼리로, 역인덱싱된 용어를 검색할 때 사용

### 유사도 스코어
스코어 계산을 위한 기본적인 알고리즘(BM25) 동작 방식을 이해하고 있다면 쿼리 요청 시 더 똑똑한 쿼리를 작성할 수 있고, 인덱스를 효율적으로 디자인할 수 있다

<br/>

```
GET ecommerce/_search
{
  "query": {
    "match": {
      "products.product_name": "Pants"
    }
  },
  "explain": true
}
```
- `"explain": true` : 쿼리 내부적인 최적화 방법과 어떤 경로를 통해 검색되었으며 어떤 기준으로 스코어가 계산되었는지 알 수 있다

#### BM25 알고리즘
TF(Term Frequecy), IDF(Inverse Document Frequency) 개념에 문서 길이를 고려한 알고리즘

##### IDF 계산
- Document Frequency : 특정 용어가 얼마나 자주 등장했는지를 의미하는 지표
  - 자주 등장하는 용어는 중요하지 않을 확률이 높다 ('to', 'the'와 같은)
  - 따라서 도큐먼트 내에서 발생 빈도가 적을수록 가중치를 높게 주는데 이를 문서 빈도의 역수(Inversse Document Frequency)라고 한다

##### TF 계산
- Term Frequency : 특정 용어가 하나의 도큐먼트에 얼마나 많이 등장했는지를 의미하는 지표
  - 특정 용어가 도큐먼트에서 많이 반복되었다면 도큐먼트의 주제와 연관되어 있을 확률이 높다
  - 따라서 특정 용어가 많이 나오면 가중치를 높인다


## 필터 컨텍스트
- '정확하게'
- 일치 여부에 따른 예/아니오의 결과를 제공
- 스코어 계산을 위해 결과를 매번 업데이트하지 않아도 되기 때문에 캐시를 이용할 수 있다
```
GET ecommerce/_search
{
  "query": {
    "bool": {
      "filter": {
        "term": {
          "day_of_week": "Friday"
        }
      }
    }
  }
}
```
- 필터 컨텍스트는 'bool` 논리 쿼리 내부의 `filter` 타입에 적용된다

<br/>

## 쿼리 사용 방법

### 쿼리 스트링

- 한 줄 정도의 간단한 쿼리에 사용
- REST API의 URI 주소에 쿼리문을 작성하는 방식
```
GET ecommerce/_search?q=customer_full_name:Mary
```
→ 'Mary'라는 용어가 포함된 도큐먼트를 검색


### Query DSL

- 한 줄에 넣기 힘든 복잡한 쿼리에 사용
- REST API 요청 본문 안에 JSON 형태로 쿼리를 작성
```
GET ecommerce/_search
{
  "query": {
    "match": {
      "customer_full_name": "Mary"
    }
  }
}
```

<br/>

## Query DSL

### 리프 쿼리
- 특정 필드에서 용어를 찾는 쿼리
- `match`, `term`, `range`, `full text`, `term level` 쿼리 등

#### `full text` 쿼리
- [Full text queries](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/full-text-queries.html)
- 전문 검색을 위해 사용되며, 전문 검색을 할 필드는 인덱스 매핑 시 텍스트 타입으로 매핑해야 한다
- 전문 검색 쿼리를 사용하면 → 검색어도 해당 필드가 인덱싱될 때 사용된 분석기에 의해 토큰으로 분리되어 → 도큐먼트 용어들과 매칭되고 → 스코어를 계산하고 검색을 한다

#### `term level` 쿼리
- [Term-level queries](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/term-level-queries.html)
- 정확히 일치하는 용어를 찾기 위해 사용되며, 인덱스 매핑 시 필드를 키워드 타입으로 매핑해야 한다. 강제는 아니지만 정확한 결과를 얻기 위한 권장사항이다
- 일반적으로 숫자, 날짜, 범주형 데이터를 정확하게 검색할 때 사용된다

#### Wildcard 쿼리
```json
GET /_search
{
  "query": {
    "wildcard": {
      "user.id": {
        "value": "ki*y",
        "boost": 1.0,
        "rewrite": "constant_score"
      }
    }
  }
}
```



### 복합 쿼리
- 복합 쿼리는 논리 쿼리로, 쿼리를 조합해 사용되는 쿼리

#### 작성 포맷
```
GET <index>/_search
{
  "query": {
    "bool": {
      "must": [
        { 쿼리문 }, ...
      ],
      "must_not": [
        { 쿼리문 }, ...
      ],
      "should": [
        { 쿼리문 }, ...
      ],
      "filter": [
        { 쿼리문 }, ...
      ]
    }
  }
}
```

### 지원하는 타입
`must`, `must_not`, `should`, `filter` 4개 타입 아래에서 전문 쿼리나 용어 수준 쿼리, 범위 쿼리, 지역 쿼리 등을 사용할 수 있다
- `must`
  - 쿼리를 실행하여 참인 도큐먼트를 찾는다
  - 복수의 쿼리를 실행하면 AND 연산을 한다
- `must_not`
  - 쿼리를 실행하여 거짓인 도큐먼트를 찾는다
  - 다른 타입과 같이 사용할 경우 도큐먼트에서 제외한다
- `should`
  - 단독으로 사용 시 쿼리를 실행하여 참인 도큐먼트를 찾는다
  - 복수의 쿼리를 실행하면 OR 연산을 한다
  - 다른 타입과 같이 사용할 경우 스코어에만 활용된다
- `filter`
  - 쿼리를 실행하여 '예/아니오' 형식의 필터 컨텍스트를 수행한다

<br/>

## 부스팅 기법 : 필드에 가중치 두기
- 여러 개의 필드 중 특정 필드에 가중치를 두는 방법
- 예를 들어, '엘라스틱'을 블로그에서 검색할 때 용어가 본문에 있는 것과 제목에 있는 것은 무게가 다르다
- 멀티 매치 쿼리에서 자주 사용된다
- 특정 필드의 스코어 값을 n배 해주는 효과를 준다

<br/>

```
GET ecommerce/_search
{
  "query": {
    "multi_match": {
      "query": "mary",
      "fields": [
        "customer_full_name^2",
        "customer_first_name",
        "customer_last_name"
      ]
    }
  }
}
```


--- 


## 범위 쿼리


<br/>

<br/>

---

<br/>

# 집계(aggregation)

## 집계 요청 기본 형태
```
GET <인덱스>/_search
{
  "aggs": {
    "my_aggs": {
      "agg_type": {
        ...
      }
    }
  }
}
```
- `aggs`: 집계 요청을 하겠다는 의미
- `my_aggs`: 사용자가 지정하는 집계 이름. 응답 결과에서 확인할 수 있다
- `agg_type`: 엘라스틱서치는 크게 메트릭 집계, 버킷 집계라는 두 가지 타입의 집계가 있다
  - 메트릭 집계
    - 통계나 계산에 사용
  - 버킷 집계
    - 도큐먼트를 그룹핑하는 데 사용

## 메트릭 집계
- 필드의 최소/최대/합계/평균/중간값 같은 통계 결과를 보여준다
- 필드 타입에 따라서 사용 가능한 집계 타입에 제한이 있다
```
GET index_1/_search
{
  "size": 0,
  "aggs": {
    "stats_aggs": {
      "avg": {
        "field": "products.base_price"
      }
    }
  }
}
```
- `size`를 0으로 설정하면 집계에 사용한 도큐먼트를 결과에 포함하지 않아 비용을 절약할 수 있다

### Cardinality
필드의 유니크한 값들의 개수를 확인하는 집계 (SQL의 `distinct coount`)
```
GET test/_search
{
  "size": 0,
  "aggs": {
    "cardi_aggs": {
      "cardinality": {
        "filed": "day_of_week",
        "precision_threshold": 100
      }
    }
  }
}
```
- `precision_threshold`: 정확도 수치
  - 값이 크면 정확도가 올라가는 대신 시스템 리소스를 많이 소모하고, 값이 작으면 정확도는 떨어지는 대신 시스템 리소스를 덜 소모한다
  - 일반적으로 `precision_threshold` 값은 카디널리티의 실제 결과보다 크게 잡아야 한다. 하지만 실제 결과를 모르기 때문에 `precision_threshold` 값을 변경해보면서 값이 변경되지 않는 임계점을 찾는 것도 방법이다
  - 기본값은 3000이며, 최대 40000까지 값을 설정할 수 있다
- 매우 적은 메모리로 집합의 원소 개수를 추정할 수 있는 HyperLogLog++ 알고리즘 기반으로 동작하며, `precision_threshold` 파라미터를 이용해 정확도와 리소스를 등가 교환한다

#### HyperLogLog++ 알고리즘
- 집합 내 중복되지 않는 항목의 개수를 세기 위한 알고리즘
- 완전히 정확한 값을 반환하지는 않으나 일반적으로 5% 이내의 오차를 보이며, 정밀도를 직접 지정해 오차율을 낮출 수 있다
- 카디널리티가 낮은, 즉 중복을 제거한 항복의 수가 적은 집합일수록 100%에 가까운 정확성을 보인다
- 집계 대상의 크기가 얼마나 크든 간에 지정한 정밀도 이상의 메모리를 사용하지 않기 때문에 엘라스틱서치와 같이 대용량 데이터베이스에서 유용한 알고리즘이다
  

## 버킷 집계
- 특정 기준에 맞춰서 도큐먼트를 그룹핑하는 역할을 한다

<br/>

---

<br/>

# Script



<br/>

---

<br/>

출처 및 참고
- 엘라스틱 스택 개발부터 운영까지(김준영, 정상운)