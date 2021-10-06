- [Spring Security](#spring-security)
  - [Authentication(인증), Authorization(인가)](#authentication인증-authorization인가)
    - [Authentication](#authentication)
      - [다양한 인증 방법](#다양한-인증-방법)
    - [Authorization](#authorization)
  - [토큰으로 인증하기](#토큰으로-인증하기)
    - [세션의 장점](#세션의-장점)
    - [세션의 단점](#세션의-단점)
    - [토큰 인증 방법](#토큰-인증-방법)
    - [토큰 인증 방법의 장점](#토큰-인증-방법의-장점)
    - [토큰 인증 방법의 단점](#토큰-인증-방법의-단점)
  - [JWT(Json Web Token)](#jwtjson-web-token)
    - [JWT의 구조](#jwt의-구조)
      - [Header](#header)
      - [Payload](#payload)

# Spring Security

- Spring 기반 애플리케이션의 보안을 담당하고 있는 프레임워크
- Spring 생태계에서 Authentication, Authorization 개념을 최대한 쉽고 유연하게 구현할 수 있도록 만들어진 프레임워크
- Spring Security의 주된 목표는 REST API endpoint, MVC url, 정적 리소스와 같은 리소스들에 접근하는 요청의 인증을 책임지는 것이다

<br/>

## Authentication(인증), Authorization(인가)

Spring Security가 궁금적으로 이루고자 하는 목표

### Authentication
- 사용자가 누구인지 확인하는 절차
- "당신은 누구입니까? 당신이 누구인지 증명하십시오"
- 인증은 서비스를 이용하는 중에도 계속 이루어져야 한다

#### 다양한 인증 방법
1. 모든 요청마다 나의 ID와 패스워드를 포함시켜서 요청한다
2. 나의 ID와 패스워드를 서버에 주고 그 응답으로 아무나 해독이 불가능한 key를 받는다(나랑 서버밖에 모르는 값) → 그 key를 모든 요청에 포함해서 보낸다

### Authorization
- 인증 이후에 리소스에 대한 권한을 통제하는 것을 의미
- "당신은 무엇을 할 수 있습니까?"
- 클라이언트가 요청한 작업이 허가된 작업인지 확인하는 절차

## 토큰으로 인증하기
세션의 단점을 해결하기 위해 사용한다.

### 세션의 장점
1. JSESSIONID는 유의미한 값이 아니고 서버에서 세션(사용자) 정보를 찾는 key로만 활용되기 때문에 탈취되었다고 해서 개인정보가 탈취된건 아니다(세션하이제킹 공격(JSESSIONID를 훔쳐서 그 유저인 것처럼 행동하는 것)에 안전하다는 것은 아님)

### 세션의 단점
1. 서버에 세션(사용자) 정보를 저장할 공간이 필요하다. 서비스를 이용하는 사용자가 많으면 저장할 공간도 많이 필요하다
2. 분산 서버에서는 세션을 공유하는데 어려움이 있다
   서버들은 세션을 공유하지 않는다. 해결 방법은 세션 서버를 두거나 서버간 세션을 공유하도록 하는 것인데 쉬운 방법은 아니다

### 토큰 인증 방법
1. 유저가 로그인을 하면 서버에서는 토큰을 생성한 뒤 저장하지 않고 (stateless) 토큰 값을 준다
2. 유저는 서버에 요청을 할 때 토큰 값을 함께 보낸다
3. 토큰은 유저를 설명할 수 있는 데이터를 포함하기 때문에 서버는 요청과 함께 받은 토큰을 의미있는 값으로 해석한다
4. 해석한 값을 토대로 유저를 인증한다

### 토큰 인증 방법의 장점
1. 세션 관리를 할 필요가 없기 때문에 별도의 저장소가 필요하지 않다
2. 서버 분산&클러스터 환경에서 확장성이 좋다

### 토큰 인증 방법의 단점
1. 한번 제공된 토큰은 회수가 어렵다. 세션의 경우 서버에서 세션을 삭제하면 브라우저의 JSESSIONID는 무용지물이 된다. 그러나 토큰은 세션을 저장하지 않기 때문에 회수할 수 없다. 그래서 보통 토큰의 유효기간을 짧게 한다
2. 토큰에는 유저의 정보가 있기 때문에 상대적으로 안정성이 우려된다. 따라서 민감정보(ex 패스워드, 개인정보)를 토큰에 포함시키면 안된다

## JWT(Json Web Token)
토큰 인증 방식에서 가장 잘 알려져있다

### JWT의 구조
HEADER.PAYLOAD.SIGNATURE

예) eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ1c2VyIiwicm9sZXMiOl.hHkvviDzMIx8EkXUTZsNLWGy51p2 bVnJknEC0HYxMaRkXGQJdIWqIX1Rv8rGK6bMq6mYyGES3jxNJVPz33wvEQ

#### Header
- JWT를 검증하는데 필요한 정보를 가진 객체
- Signature에 사용한 암호화 알고리즘이 무엇인지, key id가 무엇인지의 정보를 담고 있다
- 이 정보를 JSON으로 변환해서 UTF-8로 인코딩한 뒤, Base64 URL-Safe로 인코딩한 값이 들어있다
- Header의 값은 인코딩된 값이지 암호화된 값은 아니다

#### Payload
- 실질적으로 인증에 필요한 데이터를 저장
- 데이터의 각 필드들을 Claim이라고 한다
- 대부분의 경우 Claim에 username을 포함한다. 인증할 때 payload에 있는 username을 가져와서 유저 정보를 조회할 때 사용하기 때문이다
- 토큰 발행시간(`iat`)와 토큰 만료시간(`exp`)를 포함한다
- 이 외에도 원하는 Claim을 추가할 수 있지만 민감정보는 포함하면 안된다
- Payload 역시 Header와 마찬가지로 JSON으로 변환해서 UTF-8로 인코딩한 뒤, Base64 URL-Safe로 인코딩한 값일 뿐 암호화된 값은 아니다