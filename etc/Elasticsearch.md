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