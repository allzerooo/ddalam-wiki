- [Spring Data Redis](#spring-data-redis)
  - [의존성](#의존성)
  - [설정](#설정)
  - [데이터 직렬화](#데이터-직렬화)
    - [JSON으로 직렬화](#json으로-직렬화)
  - [TTL 설정](#ttl-설정)

# Spring Data Redis

## 의존성
```gradle
implementation 'org.springframework.boot:spring-boot-starter-data-redis'
```

이 의존성을 추가하면 application.properties의 `spring.cache.type=redis`가 기본으로 적용된다.

## 설정
Java Class로 Redis 설정을 하게되면 properties의 `spring.cache.redis.X` 설정이 작동하지 않는다.

## 데이터 직렬화
외부 저장소와 통신하기 때문에 캐싱하고 싶은 데이터를 직렬화해서 보내야 한다.

### JSON으로 직렬화
수동 설정이 필요하다

```java
@Configuration
@EnableCaching
public class RedisConfig {

	@Bean
	public RedisCacheConfiguration redisCacheConfiguration() {
		return RedisCacheConfiguration.defaultCacheConfig()
				.computePrefixWith(name -> name + ":") // cache key : value 로 나오도록
				.serializeValuesWith(SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer())); // JSON으로 직렬화
	}
}
```

## TTL 설정
application.properties로도 설정할 수 있다([설정](#설정))

```java
@Configuration
@EnableCaching
public class RedisConfig {

	@Bean
	public RedisCacheConfiguration redisCacheConfiguration() {
		return RedisCacheConfiguration.defaultCacheConfig()
				.computePrefixWith(name -> name + ":") // cache key : value 로 나오도록
				.entryTtl(Duration.ofSeconds(10)) // TTL
				.serializeValuesWith(SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer()));
	}
}
```