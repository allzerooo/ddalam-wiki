- [Server to Server](#server-to-server)
  - [`getForEntity()`, `getForObject()`](#getforentity-getforobject)
  - [`postForEntity()`, `postForObject()`](#postforentity-postforobject)
  - [`exchange()`](#exchange)

# Server to Server
Server가 Client로 다른 Server와의 연결

- RestTemplate
- WebClient

## `getForEntity()`, `getForObject()`
- 둘 다 GET 요청을 처리한다
- `getForEntity()`는 `ResponseEntity<T>`로 response를 받고, `getForObject()` 지정한 타입으로 response를 받는다

```java
public UserResponse getRequest() {
    URI uri = UriComponentsBuilder
            .fromUriString("http://localhost:9090")
            .path("/api/server/user")
            .queryParam("name", "steve")
            .queryParam("age", 10)
            .encode()
            .build()
            .toUri();

    RestTemplate restTemplate = new RestTemplate();
    String result = restTemplate.getForObject(uri, String.class); // client - server로 요청하는 지점
    ResponseEntity<UserResponse> entity = restTemplate.getForEntity(uri, UserResponse.class);

    System.out.println(entity.getStatusCode());
    System.out.println(entity.getBody());

    return entity.getBody();
}
```

`ResponseEntity` 객체는 `getStatusCode()`, `getBody()`, `getHeaders()`와 같은 메서드를 제공한다.

## `postForEntity()`, `postForObject()`
- 둘 다 POST 요청을 처리한다

```java
public UserResponse postRequest() {
    URI uri = UriComponentsBuilder
            .fromUriString("http://localhost:9090")
            .path("/api/server/user/{userId}/name/{userName}")
            .encode()
            .build()
            .expand(100, "steve") // 순서대로 path variable과 매칭 ( build() 대신 buildAndExpand() 도 사용 가능 )
            .toUri();

    // request body : object -> object mapper -> json -> rest template -> http body json
    UserRequest request = new UserRequest();
    request.setName("steve");
    request.setAge(10);

    RestTemplate restTemplate = new RestTemplate();
    ResponseEntity<UserResponse> entity = restTemplate.postForEntity(uri, request, UserResponse.class);

    return entity.getBody();
}
```

## `exchange()`
