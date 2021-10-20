- [Spring Data Elasticsearch](#spring-data-elasticsearch)
	- [Elasticsearch Operations](#elasticsearch-operations)
		- [ElasticsearchRestTemplate](#elasticsearchresttemplate)
		- [Queries](#queries)
			- [`CriteriaQuery`](#criteriaquery)
			- [`StringQuery`](#stringquery)
			- [`NativeSearchQuery`](#nativesearchquery)

# Spring Data Elasticsearch

## Elasticsearch Operations

엘라스틱서치 인덱스에 대해 호출할 수 있는 작업들이 정의된 인터페이스
- `IndexOperations`: 인덱스 생성, 삭제와 같은 인덱스 수준의 작업을 정의
- `DocumentOperations`: ID를 기반으로 엔티티를 저장, 엡데이트, 조회하는 작업을 정의
- `SearchOperations`: 쿼리를 사용해 여러 엔티티를 검색하는 작업을 정의
- `ElasticsearchOperations`: `DocumentOperations`와 `SearchOperations` 인터페이스를 결합

### ElasticsearchRestTemplate

`High Level REST Client`를 사용하는 `ElasticsearchOperations` 인터페이스의 구현체

```java
@Configuration
public class ElasticsearchConfig extends AbstractElasticsearchConfiguration {

	@Value("${elasticsearch.host}")
	private String host;

	@Value("${elasticsearch.port}")
	private String port;

	@Override
	public RestHighLevelClient elasticsearchClient() {
		ClientConfiguration clientConfiguration = ClientConfiguration.builder()
				.connectedTo(host + ":" + port)
				.build();

		return RestClients.create(clientConfiguration).rest();
	}

    // AbstractElasticsearchConfiguration는 이미 elasticsearchTemplate 빈을 제공하기 때문에 특별한 빈 생성이 필요하지 않다
}
```

### Queries
`SearchOperations` 인터페이스에 정의된 거의 모든 메서드는 검색을 위해 실행할 쿼리를 정의하는 `Query` 매개변수를 사용한다. `Query`는 인터페이스이고 Spring Data Elasticsearch는 `CriteriaQuery`, `StringQuery`, `NativeSearchQuery` 세 가지 구현을 제공한다.

#### `CriteriaQuery`

#### `StringQuery`

#### `NativeSearchQuery`