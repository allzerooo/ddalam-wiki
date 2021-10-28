- [Spring Security](#spring-security)
  - [Authentication(인증), Authorization(인가)](#authentication인증-authorization인가)
    - [Authentication](#authentication)
      - [다양한 인증 방법](#다양한-인증-방법)
    - [Authorization](#authorization)
  - [Spring Security 아키텍처](#spring-security-아키텍처)
    - [`ThreadLocal`](#threadlocal)
    - [PasswordEncoder](#passwordencoder)
      - [password 관리](#password-관리)
      - [PasswordEncoder 전략](#passwordencoder-전략)
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
      - [Signature](#signature)
    - [Key Rolling](#key-rolling)

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

## Spring Security 아키텍처

<p align="center">
    <img src="../image/Spring_Security_Architecture.png"  width="400" height="auth">
</p>

```java
SecurityContext context = SecurityContextHolder.getContext();
Authentication authentication = context.getAuthentication();
authentication.getPrincipal();
authentication.getAuthorities();
authentication.getCredentials();
authentication.getDetails();
authentication.isAuthenticated();
```

- `SecurityContextHolder`: `SecurityContext`를 제공하는 static 메서드(`getContext`)를 제공한다
- `SecurityContext`: 접근 주체와 인증에 대한 정보를 담고 있는 Context이다. 즉, `Authentication`을 담고 있다
- `Authentication`: `Principal`과 `GrantAuthority`를 제공한다. 인증이 이루어지면 해당 `Authentication`이 저장된다
- `Principal`: 유저에 해당하는 정보이다(`UserDetails`를 구현한 User Entity가 들어있다). 대부분의 경우 `Principal`로 `UserDetails`를 반환한다
- `GrantAuthority`: ROLE_ADMIN, ROLE_USER 등 `Principal`이 가지고 있는 권한을 나타낸다. prefix로 'ROLE_'이 붙는다. 인증 이후 인가를 할 때 사용하며, 권한은 여러개일 수 있기 때문에 `Collection<GrantedAuthority>` 형태로 제공한다

<br/>

Spring Security는 `Principal`, `GrantAuthority`와 같은 정보들을 사용해서 인증, 인가를 판단한다

### `ThreadLocal`
```java
public class User implements UserDetails {

    @GeneratedValue
    @Id
    private Long id;
    private String username;
    private String password;
    private String authority;

    ...
}
```
```java
public class Controller {
  @GetMapping
  public String get(Model model) {
      SecurityContext securityContext = SecurityContextHolder.getContext();
      Authentication authentication = securityContext.getAuthentication();
      Pricipal principal = authentication.getPrincipal();
      // principal에는 User 객체의 값이 담겨있다
      // id, username, password, authority

      return "get";
  }
}
```
→ 어떻게 `getContext()`를 요청한 User가 누구인지를 알고, User 마다 고유한 `SecurityContext`를 줄 수 있을까? → `ThreadLocal`(각 Thread의 고유한 영역에 저장된 **변수**로 해당 Thread 안에서만 유효한 변수)을 사용했기 때문에 가능하다

<br/>

웹 애플리케이션은 대부분의 경우 요청 1개에 Thread 1개가 생성된다(요청 1개 : Thread 1개). 이때 `ThreadLocal`을 사용하면 Thread마다 고유한 공간을 만들수 있고, 그곳에 `SecurityContext`를 저장할 수 있다(요청 1개 : Thread 1개 : Security Context 1개).

`ThreadLocal` 사용이 강제된 것은 아니며 원하면 `SecurityContext` 공유 전략을 바꿀 수 있다
- `MODE_THREADLOCAL`: 기본 설정 모드 (위 내용)
- `MODE_INHERITABLETHREADLOCAL`: 부모 Thread가 자식 Thread를 만든 경우 `SecurityContext`를 공유한다
- `MODE_GLOBAL`: 애플리케이션 전체에서 `SecurityContext`를 공유한다

### PasswordEncoder
`Principal`의 `password` 값은 PasswordEncoder encoding한 값으로, User가 입력한 값과 다른 값이 들어있다.

#### password 관리
1. 회원가입할 때 입력된 password는 암호화해서 저장해야한다
2. 로그인할 때 입력받은 password와 회원가입할 때의 password를 비교할 수 있어야한다

이 두가지를 만족하기 위해 보통 해시 함수 알고리즘을 사용한다. 해시 함수는 암호화는 비교적 쉽지만 복호화가 거의 불가능한 방식의 알고리즘이다.

#### PasswordEncoder 전략

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
- Signature에 사용한 암호화 알고리즘이 무엇인지, key의 id가 무엇인지의 정보를 담고 있다
- 이 정보를 JSON으로 변환해서 UTF-8로 인코딩한 뒤, Base64 URL-Safe로 인코딩한 값이 들어있다
- Header의 값은 인코딩된 값이지 암호화된 값은 아니다

#### Payload
- 실질적으로 인증에 필요한 데이터를 저장
- 데이터의 각 필드들을 Claim이라고 한다
- 대부분의 경우 Claim에 username을 포함한다. 인증할 때 payload에 있는 username을 가져와서 유저 정보를 조회할 때 사용하기 때문이다
- 토큰 발행시간(`iat`)와 토큰 만료시간(`exp`)를 포함한다
- 이 외에도 원하는 Claim을 추가할 수 있지만 민감정보는 포함하면 안된다
- Payload 역시 Header와 마찬가지로 JSON으로 변환해서 UTF-8로 인코딩한 뒤, Base64 URL-Safe로 인코딩한 값일 뿐 암호화된 값은 아니다

#### Signature
- JWT 토큰이 올바른 토큰이라고 서명해주는 것
- Header와 Payload는 누구나 만들 수 있는 데이터이기 때문에 토큰에 대한 진위여부를 판단할 수 없다. 마지막 Signature는 토큰에 대한 진위여부를 판단하는 용도로 Header와 Payload를 합친뒤 비밀키로 Hash를 생성해 암호화한다
- Header와 Payload를 합친 값과 비밀키를 사용해 HS512로 Hash 값을 만들어낸다. 이 값이 Signature이다

### Key Rolling
JWT의 Secret Key가 노출되면 모든 데이터가 유출될 수 있다. 이런 문제를 방지하기 위해 Secret Key를 여러개 두고, 수시로 [추가, 삭제, 변경]해서 Secret Key 중 1개가 노출되어도 다른 Secret Key와 데이터는 안전한 상태가 되도록 하는 것을 Key Rolling이라고 한다(Key Rolling이 필수는 아니다).

Secret Key 1개에 unique한 ID(kid 또는 key id라고 부름)를 연결시켜 둔다. 그리고 JWT 토큰을 만들 때 헤더에 kid를 포함해 제공하고, 서버에서 토큰을 해서할 때 kid로 Secret Key를 찾아서 Signature를 검증한다.

<br/>

--- 

<br/>

출처 및 참고
- [한 번에 끝내는 Spring 완.전.판](https://fastcampus.co.kr/dev_online_spring)