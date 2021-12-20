# Spring Cache Abstraction

- 애플리케이션에 "투명하게(transparently)" 캐시를 넣어주는 기능
  - 캐시가 시스템, 애플리케이션에 투명하게 자리잡는다는 것
    - 데이터를 통신하는 시스템 쌍방이 캐시의 존재를 모른다는 의미
    - "캐시가 있건 없건, 시스템의 기대 동작은 동일해야 한다"
    - 캐스의 목표 : 오로지 "성능"
    - 캐시의 개념과 목적에 부합하는 성질이자, 조건
      - 반복 작업이라면 고려해 보자
        - 잘 바뀌지 않는 정보를 외부 저장소에서 반복적으로 읽어온다면
        - 기대값이 어차피 같다면
        - 캐싱해서 성능 향성, I/O 감소
- 메소드, 클래스에 적용 가능
- 캐시 인프라는 Spring Boot 자동 설정으로 세팅되고, 프로퍼티로 관리 가능

## 적용 방법
1. configuration class에 `@EnableCaching`을 추가
   - main application class에 추가(`@SpringBootApplication` 애노테이션에 `@Configuration`이 포함되어 있어 configuration class로 정의되어 있기 때문에) or 따로 configuration class를 생성한 후에 `@EnableCaching`을 적용
2. 캐시를 적용하고 싶은 메서드, 클래스에 `@Cachable()`을 추가

## 캐싱에서 생각해야 하는 것들
- 무엇을 캐시할까?
- 얼마나 오랫동안 캐시할까?
- 언제 캐시를 갱신할까?

## 주요 기능들
- `@EnableCaching` : 캐시를 활성화
- `@Cacheable` : 캐시를 등록
- `@CacheEvict` : 캐시를 삭제
- `@CachePut` : 캐시를 갱신

<br/>

---

<br/>

출처 및 참고
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)