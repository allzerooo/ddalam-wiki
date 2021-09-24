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

<br/>

---

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


### 쿼리 DSL

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

## 쿼리

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