- [Spring Security](#spring-security)
  - [Spring Security가 궁극적으로 이루고자 하는 목표](#spring-security가-궁극적으로-이루고자-하는-목표)
    - [Authentication(인증)](#authentication인증)
    - [Authorization(인가)](#authorization인가)
  - [Spring Security Filter](#spring-security-filter)
    - [Filters](#filters)
  - [Spring Security 아키텍처](#spring-security-아키텍처)
    - [`ThreadLocal`](#threadlocal)
    - [PasswordEncoder](#passwordencoder)
      - [password 관리](#password-관리)
      - [PasswordEncoder 전략](#passwordencoder-전략)
  - [Spring Security Config](#spring-security-config)
    - [적용할 수 있는 Annotation](#적용할-수-있는-annotation)
  - [사용자](#사용자)
    - [메모리 사용자](#메모리-사용자)
      - [방법](#방법)
    - [데이터베이스 사용자](#데이터베이스-사용자)
  - [Authentication (인증, 로그인)](#authentication-인증-로그인)
    - [`Authentication`](#authentication)
      - [AuthenticationProvider, AuthenticationManager](#authenticationprovider-authenticationmanager)
      - [`Authentication`을 제공하는 필터들](#authentication을-제공하는-필터들)
    - [폼 로그인](#폼-로그인)
      - [`DefaultLogoutPageGeneratingFilter`](#defaultlogoutpagegeneratingfilter)
      - [`UsernamePasswordAuthenticationFilter`](#usernamepasswordauthenticationfilter)
      - [`DefaultLogoutPageGeneratingFilter`](#defaultlogoutpagegeneratingfilter-1)
      - [`LogoutFilter`](#logoutfilter)
    - [`BasicAuthenticationToken` 인증](#basicauthenticationtoken-인증)
      - [`BasicAuthenticationFilter`](#basicauthenticationfilter)
    - [OAuth2](#oauth2)
    - [JWT 토큰 (Bearer 토큰)](#jwt-토큰-bearer-토큰)
  - [인증을 유지하는 방식](#인증을-유지하는-방식)
    - [세션](#세션)
      - [SecurityContextPersistenceFilter](#securitycontextpersistencefilter)
    - [메모리그리드](#메모리그리드)
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
  - [권한 체크](#권한-체크)
    - [`FilterSecurityInterceptor`](#filtersecurityinterceptor)
    - [`ExceptionTranslationFilter`](#exceptiontranslationfilter)
      - [`AuthenticationEntryPoint`](#authenticationentrypoint)
      - [`AuthenticationException`](#authenticationexception)
  - [Authorization](#authorization)
    - [권한 처리에 관여하는 것들](#권한-처리에-관여하는-것들)
    - [인증과 권한의 구조](#인증과-권한의-구조)
    - [메서드 후처리](#메서드-후처리)
    - [temp](#temp)
      - [다양한 인증 방법](#다양한-인증-방법)

# Spring Security

- Spring 기반 애플리케이션의 보안을 담당하고 있는 프레임워크
- Spring 생태계에서 Authentication, Authorization 개념을 최대한 쉽고 유연하게 구현할 수 있도록 만들어진 프레임워크
- Spring Security의 주된 목표는 REST API endpoint, MVC URL, 정적 리소스와 같은 리소스들에 접근하는 요청의 인증을 책임지는 것이다

<br/>

## Spring Security가 궁극적으로 이루고자 하는 목표

### Authentication(인증)
- 사용자가 누구인지 확인하는 절차
- "당신은 누구입니까? 당신이 누구인지 증명하십시오"
- 인증은 서비스를 이용하는 중에도 계속 이루어져야 한다

### Authorization(인가)
- 인증 이후에 리소스에 대한 권한을 통제하는 것을 의미
- "당신은 무엇을 할 수 있습니까?"
- 클라이언트가 요청한 작업이 허가된 작업인지 확인하는 절차

<br/>

## Spring Security Filter
<p align="center">
    <img src="../image/servlet_container_2.png"  width="800" height="auth">
</p>

<p align="center">
    <img src="../image/spring_security_filter.png"  width="600" height="auth">
</p>

Spring Security는 여러 security 정책이 공존할 수 있다. 경우에 따라 정책이 선택돼기 때문에 필터 체인에 어떤 정책을 넣어두기가 어렵다. 따라서, Spring Security는 `DelegatingFilterProxy`라는 필터를 만들어 메인 필터 체인에 끼워두고, Proxy가 다시 Security Filter Chain 그룹을 선택하도록 동작한다. 

`WebSecurityConfigurerAdapter`의 `configure(HttpSecurity http)` 메서드에서 안에서 설정하는 내용이 Security Filter Chain을 구성하는 것이라고 볼 수 있다. 두 개 이상의 필터 체인을 구성하고 싶으면 `WebSecurityConfigurerAdapter`를 상속받는 클래스를 여러개 정의하면 되는데 이 때 어떤 순서로 request를 처리할 것인지 필터의 순서가 중요하기 때문에 반드시 클래스에 `@Order`를 추가해주어야 한다.

### Filters
Security Filter Chain에는 다양한 필터들이 들어갈 수 있다. 필터는 넣거나 뺄 수 있고, 순서를 조정할 수 있지만 필터의 순서가 매우 critical 할 수 있기 때문에 기본 필터들은 그 순서가 어느정도 정해져 있다.

<p align="center">
    <img src="../image/spring_security_filter_list.png"  width="600" height="auth">
</p>

각 필터는 서로 다른 관심사를 해결한다.

- HeaderWriterFilter : Http 해더를 검사한다. 써야 할 건 잘 써있는지, 필요한 해더를 더해줘야 할 건 없는가?
- CorsFilter : 허가된 사이트나 클라이언트의 요청인가?
- CsrfFilter : POST, PUT과 같이 리소스 변경 요청의 경우 내가 내보낸 리소스에서 올라온 요청인가?
- LogoutFilter : 지금 로그아웃하겠다고 하는건가?
- UsernamePasswordAuthenticationFilter : username / password 로 로그인을 하려고 하는가? 만약 로그인이면 여기서 처리하고 가야 할 페이지로 보내준다
- ConcurrentSessionFilter : 동시에 여러군데서 로그인 하는걸 허용할 것인가?
- BearerTokenAuthenticationFilter : Authorization 해더에 Bearer 토큰이 오면 인증 처리 해준다
- BasicAuthenticationFilter : Authorization 해더에 Basic 토큰을 주면 검사해서 인증처리 해준다
- RequestCacheAwareFilter : 방금 요청한 request 이력이 다음에 필요할 수 있으니 캐시에 담아놓는다
- SecurityContextHolderAwareRequestFilter : 보안 관련 Servlet 3 스펙을 지원하기 위한 필터라고 한다
- RememberMeAuthenticationFilter : 아직 Authentication 인증이 안된 경우라면 RememberMe 쿠키를 검사해서 인증 처리해준다
- AnonymousAuthenticationFilter : 아직도 인증이 안되었으면 Anonymous 사용자라고 처리
- SessionManagementFilter : 서버에서 지정한 세션정책을 검사
- ExcpetionTranslationFilter : 이 이후에 인증이나 권한 예외가 발생하면 잡아서 처리해 해준다
- FilterSecurityInterceptor : 여기까지 살아서 왔다면 인증이 있다는 거니, 들어가려고 하는 request에 들어갈 자격이 있는지 그리고 리턴한 결과를 보내줘도 되는건지 마지막으로 점검해준다
- 그 밖에 OAuth2나 Saml2, Cas, X509 등에 관한 필터들도 있다

경우에 따라 필터를 만들고 추가해 사용할수도 있다.

<br/>

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

<br/>

## Spring Security Config
- `WebSecurityConfigurerAdapter`를 상속해 구현하는 config class
- 기본적으로 모든 요청은 다 막혀있다
  ```java
  http.authorizeRequests((requests) ->
                  requests.anyRequest().authenticated());
  ```
  - `antMatchers()`를 사용해서 요청 마다 인증 수준을 설정할 수 있다
    ```java
    http.authorizeRequests((requests) ->
                requests.antMatchers("/").permitAll()   // "/" 주소는 모든 사용자에게 접근을 허락
                        .anyRequest().authenticated()   // 나머지는 인증해야 접근을 허락
        );
    ```

### 적용할 수 있는 Annotation
- `@Order(1)` : security filter chain을 여러개 구성할 경우 순서 지정
- `@EnableWebSecurity`
  - `@EnableWebSecurity(debug = true)` : security 필터를 어떻게 구성했는지 터미널 로그로 확인할 수 있음
- `@EnableGlobalMethodSecurity`
  - `@EnableGlobalMethodSecurity(prePostEnabled = true)` : 권한 체크 모듈이 작동됨
  - `@PreAuthorize("hasAnyAuthority('ROLE_USER')")` 이와 같은 코드를 추가한 곳에서 권한 체크가 작동된다

<br/>

## 사용자
### 메모리 사용자
간단히 특정된 소스를 위한 서비스나 테스트를 위한 사용자

#### 방법

1. 기본 사용자 로그인 (처음 설정되어 있는)
  - ID : user
  - PW : Spring Boot 실행 시 콘솔에 패스워드가 나온다
    <p align="left">
      <img src="../image/spring_security_default_pw.png"  width="480" height="auto">
    </p>
2. `application.yml`에 설정하기
   ```yml
    spring:
      security:
        user:
          name: user1
          password: 1111
          roles: USER
   ```
   yml에는 사용자를 한 명만 추가할 수 있다
3. `WebSecurityConfigurerAdapter` 사용하기
    
    `WebSecurityConfigurerAdapter` 사용해 사용자를 추가하면 yml에 추가한 사용자는 더 이상 인식되지 않는다

    ```java
      public class SecurityConfig extends WebSecurityConfigurerAdapter {

        @Override
        protected void configure(AuthenticationManagerBuilder auth) throws Exception {
            auth.inMemoryAuthentication()
                    .withUser(User.builder()
                                .username("user2")
                                .password(passwordEncoder().encode("2222"))   // 패스워드를 인코딩하지 않으면 에러가 난다 -> passwordEncoder() 추가
                                .roles("USER"))
                    .withUser(User.builder()
                                .username("admin")
                                .password(passwordEncoder().encode("3333"))
                                .roles("ADMIN"));
        }

        @Bean
        PasswordEncoder passwordEncoder() {
            return new BCryptPasswordEncoder(); // Spring Security가 기본으로 사용하는 인코더
        }
      }
    ```
### 데이터베이스 사용자
UserDetails를 구현한 User 객체와 UserDetailsService를 구현하면 Spring Security가 나머지는 쉽게 쓸 수 있도록 지원해준다.

<p align="center">
  <img src="../image/spring_security_dao_authentication_provider.png"  width="600" height="auto">
</p>
<p align = "center" style="font-size: 12px; corlor: #a2a6a3;">SecurityConfig 블럭 내부 UsernamePasswordAuthenticationToken 오타 → UsernamePasswordAuthenticationFilter</p>

UsernamePasswordAuthenticationFilter는 UsernamePasswordAuthenticationToken를 ProviderManager에게 넘겨준다. 기본적으로는 DaoAuthenticationProvider가 UsernamePasswordAuthenticationToken를 처리해주는데 이 때 UserDetailsService 빈이 있으면 토큰을 넘겨서 UserDetails라는 Principal 객체를 받도록 되어있다. 인증이 된 사용자라면 Principal 객체를 UsernamePasswordAuthenticationToken에 넣어서 리턴을 한다.

<br/>

## Authentication (인증, 로그인)

로그인을 했다는 것은 `authenticated`가 `true`인 `Authentication` 객체를 `SecurityContext`에 갖고 있는 상태를 말한다. 단, `Authentication`이 `AnonymousAuthenticationToken`이 아니어야 한다.

### `Authentication`

<p align="center">
  <img src="../image/spring_security_authentication_structure.png"  width="600" height="auto">
</p>

<p align="center">
  <img src="../image/spring_security_authentication_structure_2.png"  width="500" height="auto">
</p>

- 일종의 통행증 같은 것. 인증된 사용자니까 접근 권한이 있는 곳에 접근할 수 있다는
- `Authentication`은 인터페이스이고, 구현체들은 대부분 authenticationToken이라고 네이밍 되어있다
- Authentication 객체는 SecurityContetxtHolder를 통해 세션이 있건 없건 언제든 접근할 수 있도록 필터체인에서 보장해준다
- 가지고 있는 정보들
  - Set<GrantedAuthority> authorities : 인증된 권한 정보
    - 어떤 권한을 가지는지를 나타내는 `GrantedAuthority` 인터페이스를 구현한 객체들을 가짐
  - principal : 인증 대상(인증 해줘야될 객체)에 관한 정보. 주로 UserDetails 객체가 옴
  - credentials : 인증을 받기 위해 필요한 정보. 주로 비밀번호가 오지만, 인증 후에는 보안을 위해 삭제함
  - details : request에 대한 detail 정보. 그 밖에 필요한 정보. IP, 세션정보, 기타 인증요청에서 사용했던 정보들
  - boolean authenticated : 인증이 되었는지를 체크함


#### AuthenticationProvider, AuthenticationManager

<p align="center">
  <img src="../image/spring_security_authentication_provider.png"  width="400" height="auto">
</p>

<p align="center">
  <img src="../image/spring_security_authentication_manager.png"  width="500" height="auto">
</p>

- AuthenticationProvider : `Authentication` 제공자
  - Authentication을 받아서 인증을 하고 인증된 결과를 다시 Authentication 객체로 전달한다
- AuthenticationManager : AuthenticationProvider들을 관리
  - ProviderManager : AuthenticationManager의 구현체, 복수개 존재할 수 있다
  - AuthenticationManager 직접 정의해서 제공하지 않는다면, AuthenticationManager를 만드는 `AuthenticationManagerFactoryBean`에서 `DaoAuthenticationProvider`를 기본 provider로 등록한 AuthenticationManager를 만든다
  - DaoAuthenticationProvider는 반드시 1개의 UserDetailsService를 발견할 수 있어야 한다. 만약 없으면 InmemoryUserDetailsManager [(메모리 사용자)](#메모리-사용자)에 의해 기본 사용자가 등록되어 제공된다


Authentication은 인증을 받기 위한 정보, 인증을 하기 위한 정보, 인증 결과가 하나의 객체에 다 들어있다. 인증을 제공해줄 제공자(AuthenticationProvider)가 어떤 인증에 대해 허가를 내줄 것인지 판단하기 위해 입력된 인증 정보를 보고 허가된 인증을 내어주는 방식이다. 그래서 AuthenticationProvider는 처리 가능한 Authentication에 대해 알려주는 support 메서드를 지원하고, authenticate()에서 Authentication을 입력 값과 동시에 출력 값으로도 사용한다.
예를 들어, StudentManager라는 커스텀 AuthenticationProvider를 만들어본다면
```java
  public class StudentManager implements AuthenticationProvider, InitializingBean {

      // StudentManager는 Student리스트를 가지고 있어야 된다. 따라서, 원래는 여기에서 DB 조회를 한다
      private HashMap<String, Student> studentDB = new HashMap<>();

      // Authentication Token을 StudentAuthenticationToken으로 발행해주기
      @Override
      public Authentication authenticate(Authentication authentication) throws AuthenticationException {
          StudentAuthenticationToken token = (StudentAuthenticationToken) authentication;
          if (studentDB.containsKey(token.getCredentials())) {
              Student student = studentDB.get(token.getCredentials());
              // studentAuthenticationToken을 만들어서 return
              return StudentAuthenticationToken.builder()
                      .principal(student)
                      .details(student.getUsername())
                      .authenticated(true)
                      .build();
          }
          return null;
      }

      @Override
      public boolean supports(Class<?> authentication) {
          // StudentAuthenticationToken을 처리할 수 있는 provider이다
          return authentication == StudentAuthenticationToken.class;
      }
  }
```

#### `Authentication`을 제공하는 필터들
- UsernamePasswordAuthenticationFilter : 폼 로그인 -> UsernamePasswordAuthenticationToken
- RememberMeAuthenticationFilter : remember-me 쿠키 로그인 -> RememberMeAuthenticationToken
- AnonymousAuthenticationFilter : 로그인하지 않았다는 것을 인증함 -> AnonymousAuthenticationToken
- SecurityContextPersistenceFilter : 기존 로그인을 유지함(기본적으로 session을 이용함. session은 `Authentication`을 담고 있다가 이후 로그인에서 이 필터를 통해 `SecurityContextHolder`에 `Authentication`을 다시 넣어준다)
- BearerTokenAuthenticationFilter : JWT 로그인
- BasicAuthenticationFilter : ajax 로그인(`Authorization` 헤더에 username/password를 담아서 Base64로 인코딩해서 보내면 이 필터에서 인증 후 바료 요청을 수행해준다. 로그인 페이지가 필요없는 경우에, session을 사용하는 경우에 많이 사용) -> UsernamePasswordAuthenticationToken
- OAuth2LoginAuthenticationFilter : 소셜 로그인 -> OAuth2LoginAuthenticationToken, OAuth2AuthenticationToken
- OpenIDAuthenticationFilter : OpenID 로그인
- Saml2WebSsoAuthenticationFilter : SAML2 로그인
- ... 기타

### 폼 로그인

#### `DefaultLogoutPageGeneratingFilter`
- 별도의 로그인 페이지를 설정하지 않았을 때 제공되는 필터
- GET /login을 처리해 기본 로그인 폼이 있는 페이지를 제공한다

  ```java
  public class DefaultLogoutPageGeneratingFilter extends OncePerRequestFilter {

    private RequestMatcher matcher = new AntPathRequestMatcher("/logout", "GET");

    ...

    private void renderLogout(HttpServletRequest request, HttpServletResponse response) throws IOException {
      StringBuilder sb = new StringBuilder();
      sb.append("<!DOCTYPE html>\n");
      sb.append("<html lang=\"en\">\n");
      sb.append("  <head>\n");
      sb.append("    <meta charset=\"utf-8\">\n");
      sb.append("    <meta name=\"viewport\" content=\"width=device-width, initial-scale=1, shrink-to-fit=no\">\n");
      sb.append("    <meta name=\"description\" content=\"\">\n");
      sb.append("    <meta name=\"author\" content=\"\">\n");
      sb.append("    <title>Confirm Log Out?</title>\n");
      sb.append("    <link href=\"https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/css/bootstrap.min.css\" "
          + "rel=\"stylesheet\" integrity=\"sha384-/Y6pD6FV/Vv2HJnA6t+vslU6fwYXjCFtcEpHbNJ0lyAFsXTsjBbfaDjzALeQsN6M\" "
          + "crossorigin=\"anonymous\">\n");
      sb.append("    <link href=\"https://getbootstrap.com/docs/4.0/examples/signin/signin.css\" "
          + "rel=\"stylesheet\" crossorigin=\"anonymous\"/>\n");
      sb.append("  </head>\n");
      sb.append("  <body>\n");
      sb.append("     <div class=\"container\">\n");
      sb.append("      <form class=\"form-signin\" method=\"post\" action=\"" + request.getContextPath()
          + "/logout\">\n");
      sb.append("        <h2 class=\"form-signin-heading\">Are you sure you want to log out?</h2>\n");
      sb.append(renderHiddenInputs(request)
          + "        <button class=\"btn btn-lg btn-primary btn-block\" type=\"submit\">Log Out</button>\n");
      sb.append("      </form>\n");
      sb.append("    </div>\n");
      sb.append("  </body>\n");
      sb.append("</html>");
      response.setContentType("text/html;charset=UTF-8");
      response.getWriter().write(sb.toString());
    }

    ...

  }
  ```

#### `UsernamePasswordAuthenticationFilter`
- POST /login 을 처리
  ```java
    http.formLogin()
  ```
  위 내용에 설정된다
- 주요 설정 정보
  - filterProcessingUrl : 로그인을 처리해 줄 URL (POST) (기본 값은 /login)
  - username parameter : POST에 username에 대한 값을 넘겨줄 인자의 이름
  - password parameter : POST에 password에 대한 값을 넘겨줄 인자의 이름
  - 로그인 성공시 처리 방법
    - defaultSuccessUrl : 로그인 성공 시 이동할 URL, alwaysUse 옵션 설정이 중요 (false로 설정하지 않으면 루트 페이지로 이동, false로 설정할 경우 로그인을 요청했던 페이지로 이동)
    - successHandler
  - 로그인 실패시 처리 방법
    - failureUrl :  default URL은 "/login?error"
    - failureHandler
  - authenticationDetailSource : Authentication 객체의 details 에 들어갈 정보를 직접 만들어 줌.
    ```java
      @Component
      public class CustomAuthDetails implements AuthenticationDetailsSource<HttpServletRequest, RequestInfo> {    // 넘겨줄 객체인 RequestInfo는 원하는대로 생성한 것

          @Override
          public RequestInfo buildDetails(HttpServletRequest request) {
              // 로그인이 일어날 때 정보를 가져다가 RequestInfo에 넣어서 보내주기
              return RequestInfo.builder()
                      .remoteIp(request.getRemoteAddr())
                      .sessionId(request.getSession().getId())
                      .loginTime(LocalDateTime.now())
                      .build();
          }
      }

      public class SecurityConfig extends WebSecurityConfigurerAdapter {

        private final CustomAuthDetails customAuthDetails;

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http.formLogin(
                    login -> login.loginPage("/login")
                    .authenticationDetailsSource(customAuthDetails)
                );
        }
      }
    ```
- 주요 로직
  ```java
    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response)
        throws AuthenticationException {
      if (this.postOnly && !request.getMethod().equals("POST")) {
        throw new AuthenticationServiceException("Authentication method not supported: " + request.getMethod());
      }
      String username = obtainUsername(request);
      username = (username != null) ? username : "";
      username = username.trim();
      String password = obtainPassword(request);
      password = (password != null) ? password : "";
      UsernamePasswordAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(username, password);
      // Allow subclasses to set the "details" property
      setDetails(request, authRequest);
      return this.getAuthenticationManager().authenticate(authRequest); // authentication manager에게 인증을 위임 → authentication provider들에게 인증을 처리할 수 있는지 물어봄(?) → authentication provider들 중 하나라도 인증을 처리할 수 있다면 그 결과로 authentication을 넘겨줌
    }
  ```
  - request에서 username, password를 꺼내서 `UsernamePasswordAuthenticationToken`을 생성하는데 이때까지는 authenticated가 false인 상황
  - `this.getAuthenticationManager().authenticate(authRequest);` 이 때 details가 필요할 수 있어서 위 라인에서 `setDetails`를 한다
  - `UsernamePasswordAuthenticationFilter`를 커스터마이징 해야 될 때가 있는데 그 때 details에 들어갈 정보를 직접 넣어줄 수 있도록 `authenticationDetailSource` 제공된다

#### `DefaultLogoutPageGeneratingFilter`
- 별도의 로그아웃 페이지를 설정하지 않았을 때 제공되는 필터
- GET /logout을 처리해 기본 로그아웃 폼이 있는 페이지를 제공한다

#### `LogoutFilter`
- POST /logout이 요청되었을 때 동작하는 filter
- logout URL을 변경할 수 있다
  ```java
    private void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
			throws IOException, ServletException {
      if (requiresLogout(request, response)) { // logout URL일 때만 처리, 나머지는 통과
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        if (this.logger.isDebugEnabled()) {
          this.logger.debug(LogMessage.format("Logging out [%s]", auth));
        }
        this.handler.logout(request, response, auth); // handler에게 logout 처리 요청
        this.logoutSuccessHandler.onLogoutSuccess(request, response, auth);
        return;
      }
      chain.doFilter(request, response);
    }
  ```
- `LogoutHandler`
  ```java
    public interface LogoutHandler {

      void logout(HttpServletRequest request, HttpServletResponse response, Authentication authentication);

    }
  ```
  - 구현체들
    - SecurityContextLogoutHandler : 세션과 SecurityContext 를 clear 함.
    - CookieClearingLogoutHandler : clear 대상이 된 쿠키들을 삭제함.
    - CsrfLogoutHandler : csrfTokenRepository 에서 csrf 토큰을 clear 함.
    - HeaderWriterLogoutHandler
    - RememberMeServices : remember-me 쿠키를 삭제함.
    - LogoutSuccessEventPublishingLogoutHandler : 로그아웃이 성공하면 이벤트를 발행함.
- `LogoutSuccessHandler`
  - `LogoutHandler`가 처리 성공 시 `LogoutSuccessHandler`에게 그 다음 처리를 넘김
  ```java
    public interface LogoutSuccessHandler {

      void onLogoutSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication)
          throws IOException, ServletException;

    }
  ```
  - 구현체 
    - SimpleUrlLogoutSuccessHandler

### `BasicAuthenticationToken` 인증

#### `BasicAuthenticationFilter`

<p align="center">
  <img src="../image/spring_security_basic_authentication_fiter.png"  width="500" height="auto">
</p>

<p align="center">
  <img src="../image/spring_security_basic_authentication_spa.png"  width="500" height="auto">
</p>

- 기본적으로 로그인 페이지를 사용할 수 없는 상황에서 사용
- 요청 헤더의 Autorization 필드에 ID/PW를 Base64로 인코딩한 Basic 토큰 값을 넣어서 보내면 BasicAuthenticationFilter가 헤더에서 이 토큰을 읽어서 인증을 시도한 다음, 인증이 되면 인증 토큰을 SecurityContextHolder에 넣어두는 작업을 한다
- 토큰에 ID/PW가 포함되기 때문에 보안에 취약하며 https 프로토콜을 사용할 것을 권장한다
- 보통 session을 사용할 때 BasicAuthenticationToken을 함께 사용한다. 로그인 되면 session에 Authentication이 저장되며, RememberMe를 설정한 경우에는 remember-me 쿠키가 브라우저에 저장되기 때문에 장시간 로그인을 거치지 않고 이용할 수 있어서 최초 로그인시에만 인증을 처리하도록 사용한다
- 에러가 나면 401 (UnAuthorized) 에러를 보낸다
- 기본적으로 UsernamePasswordAuthenticationToken을 사용해 인증한다
- 설정 방법
  ```java
    public class SecurityConfig extends WebSecurityConfigurerAdapter {

      @Override
      protected void configure(HttpSecurity http) throws Exception {
          http
              .httpBasic();
      }
    }
  ```
### OAuth2

### JWT 토큰 (Bearer 토큰)

## 인증을 유지하는 방식

### 세션
톰캣과 같은 WAS가 지원하는 세션을 이용하는 방법

<br/>

Spring Security는 세션에 관계없이 Authentication이라는 통행증을 발급, 다음 요청이 왔을 때 이 Authentication을 어떻게 유지시켜 줄 것인가라는 관심사를 가지고 있다. 이를 위해 Spring Security는 `SecurityContextPersistenceFilter`, `RememberMeAuthenticationFilter`, `AnonymousAuthenticationFilter` 등과 같이 인증을 보조해주는 필터를 제공한다.

#### SecurityContextPersistenceFilter
- SecurityContext를 저장하는 SecurityContextRepository를 가지고 있다
- SecurityContextRepository는 여러 구현체가 있는데 기본적으로 HttpSessionSecurityContextRepository로 되어있다
- 인증을 하게되면 그 이후부터는 Session에서 SecurityContext를 가져다가 SecurityContextHolder에 넣어준다
- 그 이후 필터들은 SecurityContextHolder에 SecurityContext가 있기 때문에 인증이 된 것으로 간주한다
- ThreadLocal이 작업을 완료하면 SecurityContext를 클리어 해준다


<p align="center">
  <img src="../image/spring_security_security_context_persistance_filter.png"  width="500" height="auto">
</p>

### 메모리그리드
세션을 이용하기 어려운 경우 Redis나 DB를 이용해서 인증을 유지하는 방법
- OAuth2, JWT 토큰을 사용하는 경우는 서버를 스케일 아웃할 것을 고려하는 큰 서비스이기 때문에 메모리그리드 방법을 고려하게 된다

<br/>

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

<br/>

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

## 권한 체크

### `FilterSecurityInterceptor`

<br/>
스프링의 권한을 체크하는 것들을 SecurityInterceptor라고 한다

<p align="center">
    <img src="../image/spring_security_security_interceptor.png"  width="800" height="auth">
</p>

컨트롤러에 들어오는 요청은 컨트롤러 앞에서 인터셉터가 가로채서 컨트롤러를 사용할 수 있는지 검사를 하고, 서비스 레이어에서도 인터셉터들이 동작을 한다. 

<p align="center">
    <img src="../image/spring_security_filter_security_interceptor.png"  width="800" height="auth">
</p>

`FilterSecurityInterceptor`는 `FilterSecurityInterceptor`와 `MethodSecurityInterceptor`로 나뉜다. `FilterSecurityInterceptor`는 필터에서 권한 검사를 하고, `MethodSecurityInterceptor`는 메서드에서 권한 검사를 한다. `FilterSecurityInterceptor`는 security config에서 설정한 권한 정보들을 참고해 체크를 한다. security 입장에서는 인증된 이후 최초의 방어선인 것이다. 그 이후 컨트롤러 레이어, 서비스 레이어에서 보안을 2차, 3차 방어하게 된다.

`AbstractSecurityInterceptor`의 `AccessDecisionManager`는 권한 검사를 해주는 매니저이다. `AccessDecisionManager` 밑의 Access voter들이 투표를 한다. 만약 해당 지점을 통과할 수 없다면 Access Denied를 발생시킨다. 필터에 선언된 권한들이라면 `FilterSecurityInterceptor`에서 권한 검사를 하고, 메서드에 선언된 권한들이라면 `MethodSecurityInterceptor`에서 권한 검사를 한다. 

```java
http
  .authrizeRequest(request -> 
    request
      .antMatchers("/").permitAll()
      .antMatchers("/admin/**").hasRole("ADMIN")
      .anyRequest().authenticated()
  )
```
이렇게 구성된게 `FilterSecurityInterceptor`에서 검사하는 것들이다. 디테일한 것들은 메서드에 애노테이션(`@PreAuthorize`, `@PostAuthorize` 와 같은)을 사용해 `MethodSecurityInterceptor`에서 처리하도록 한다.


<p align="center">
    <img src="../image/spring_security_filter_security_interceptor_invoke.png"  width="800" height="auth">
</p>

- beforeInvodation : Security Config 에서 설정한 접근 제한을 체크합니다.
- finallyInvocation : RunAs(임시 권한을 부여하는) 권한을 제거합니다.
- afterInvocation : AfterInvocationManager 를 통해 체크가 필요한 사항을 체크합니다. 특별히 설정하지 않으면 AfterInvocationManager 는 null 입니다.

### `ExceptionTranslationFilter`
`FilterSecurityInterceptor`나 애플리케이션에서 발생한 오류를 가로채 처리하는 작업을 한다
- `AuthenticationException`과 `AccessDeniedException`만 처리한다
- 그 밖의 오류는 보통 ControllerAdvice를 이용해서 처리하는 것을 권장한다

<p align="center">
    <img src="../image/spring_security_exception_translation_filter.png"  width="800" height="auth">
</p>

401 에러와 403 에러에 대응한다고 볼 수 있다

- 401 : 인증 실패
  - AuthenticationException
  - 다시 로그인 해야 하므로 AuthenticationEntryPoint 로 처리를 넘깁니다.
  - AuthenticationException이 발생했다고 서버가 반드시 401 에러를 내려보내는 것은 아닙니다. 해당 에러를 401 오류로 처리하는 코드를 넣어햐 합니다. 필요에 따라서는 403 오류코드로 처리하기도 합니다.

- 403 : 권한 없음
  - AccessDeniedException
  - Anonymous 유저이거나 RememberMe 유저이면 다시 로그인 하도록 AuthenticationEntryPoint 로 처리를 넘깁니다.
  - 그 밖의 유저는 권한없음 페이지로 이동하거나 권한없음 메시지를 받습니다.

#### `AuthenticationEntryPoint`
- void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException;
- 웹페이지라면 로그인 페이지로 이동하고, 클라이언트 애플리케이션이라면 재로그인하라는 메시지를 보냅니다.

#### `AuthenticationException`
- InsufficientAuthenticationException : credential 값이 부족할 때
- AccountStatusException :
  - LockedException
  - CredentialsExpiredException
  - DisabledException
  - LockedException
- AuthenticationCredentialsNotFoundException : 서블릿 컨텍스트에서 Authentication이 없을 때
- AuthenticationServiceException : 서버 문제로 인증 서비스를 처리해 줄 수 없을 때. (500)
- BadCredentialsException : credentials 값이 invalid 할 때
- RememberMeAuthenticationException
  - CookieTheftException
  - InvalidCookieException
- InsufficientAuthenticationException :
- AuthenticationServiceException
  - InternalAuthenticationServiceException
- NonceExpiredException
- ProviderNotFoundException
- PreAuthenticatedCredentialsNotFoundException
- SessionAuthenticationException
- UsernameNotFoundException

<br/>

## Authorization

<p align="center">
    <img src="../image/spring_security_authorization.png"  width="800" height="auth">
</p>

- SecurityFilerChain 당 한개의 FilterSecurityInterceptor를 둘 수 있고, 각 SecurityInterceptor당 한개의 AccessDecisionManager 를 둘 수 있습니다. 반면 Method 권한 판정은 Global 한 권한 위원회를 둡니다. 그래서 GlobalMethodSecurityConfiguration 을 통해 AccessDecisionManager 를 설정합니다.
- 인증이 모든 요청에 대해 공통적으로 처리해야 하는 것인데 반해 권한은 상황상황에 맞게 처리해야 하는 특징이 있습니다.
- 그래서 인증을 처리하는 코드는 필터와 어울리고, 권한은 interceptor 와 어울려 동작합니다. 필터는 servlet container 가 제공하는 구조를 스프링이 자체 filterchain 을 만들어서 관리하는 방식으로 처리하고 있고, interceptor 는 스프링이 빈을 등록하고 프락시 객체를 가지고 엮어주는 과정에서 각 PointCut에 의해 구분된 JoinPoint에 interceptor 가 Advice 하는 메커니즘으로 작동합니다.
- 필터 위에 상주하는 Interceptor 를 FilterSecurityInterceptor라 하고 Method 위에 annotation의 형태로 상주하는 Interceptor 를 MethodSecurityInterceptor 라고 합니다. FilterInterceptor 는 필터 설정에서 설정하고 MethodInterceptor 는 annotation 으로 설정합니다. @EnableGlobalMethodSecurity 를 설정해줘야 MethodSecurityInterceptor 가 동작합니다.

### 권한 처리에 관여하는 것들
- 접근하려고 하는 사람이 어떤 접근 권한을 가지고 있는가?
  - GrantedAuthority
    - Role Based
    - Scope Based
    - User Defined
- 접근하려고 하는 상황에서는 체크해야 할 내용은 무엇인가?
  - SecurityMetadataSource, ConfigAttribute
  - 정적인 경우와 동적인 경우
  - AccessDecisionVoter 가 vote 해줌
- 여러가지 판단 결과가 나왔을 때 취합은 어떤 방식으로 할 것인가?
  - AccessDecisionManager : 권한 위원회
    - AffirmativeBased : 긍정 위원회
    - ConsensusBased : 다수결 위원회
    - UnanimouseBased : 만장일치 위원회

### 인증과 권한의 구조 

<p align="center">
    <img src="../image/spring_security_authentication_authorization_architecture.png"  width="800" height="auth">
</p>

- 인증이 AuthenticationFilter 를 가지고 Authentication을 발급해주는 관계였다면, 권한은 SecurityInterceptor 에서 Access Granted 와 Denied 를 판정하는 결과를 만들어 내는 대응 관계를 가지고 있습니다.
- 인증이 제공해 주는 권한과 각 Interceptor가 위치한 포인트의 조건들(ConfigAttribute) 들을 가지고 판정을 내려주는 Voter 들에 따라 Granted / Denied 가 구분이 됩니다. 그렇지만 권한은 인증보다 훨씬 상황이 다양하다고 볼 수 있습니다.
- AccessDecisionManager 는 인터페이스입니다. 반드시 Voter 를 구현해서 처리해야 할 필요는 없습니다. 솔직히 Application을 구현한다면 Voter 없이 구현하는 것이 간단할 수 있습니다.

### 메서드 후처리
메서드가 결과를 리턴한 다음 리턴된 객체에 사용자가 접근할 수 있는지

- `MethodSecurityInterceptor`가 담당
  - 중요한 멤버 변수
    - `AccessDecisionManager` : 사전 체크를 담당. `@Secured`나 `@PreAuthorize`, `@PreFilter`를 처리
    - `AfterInvocationManager`
      - `@PostAuthorize`, `@PostFilter`를 처리
      - Authentication만 가지고는 권한 체크를 충분히 했다고 볼 수 없다. 보통 어떤 객체의 값을 변경해야 하는 경우에는 메서드에 들어오기 전에, 값을 조회하려고 하는 경우에는 값을 가져온 이후에 각각 접근 권한을 체크해줘야 한다
      - 체크해야 할 대상이 한개라면 Pre/PostAuthorized로 체크를 하면 되지만, 대상이 복수개라면 보통은 리스트로 묶이기 때문에 대상을 filtering을 해서 들어가거나 넘겨야 한다
      - 물론 메서드를 처리하는 중간에 권한을 검사해야 하는 경우도 있다. 이 경우는 Proxy 빈의 특징을 잘 파악해서 메서드 간에 권한 검사가 충분히 이루어지도록 annotation을 설계해서 처리할 수 있다
    - `RunAsManager`

<br/>

--- 

<br/>

출처 및 참고
- [한번에 끝내는 Java/Spring 웹 개발 마스터 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_javaend)
- [한 번에 끝내는 Spring 완.전.판](https://fastcampus.co.kr/dev_online_spring)

### temp
#### 다양한 인증 방법
1. 모든 요청마다 나의 ID와 패스워드를 포함시켜서 요청한다
2. 나의 ID와 패스워드를 서버에 주고 그 응답으로 아무나 해독이 불가능한 key를 받는다(나랑 서버밖에 모르는 값) → 그 key를 모든 요청에 포함해서 보낸다
3. 소셜 로그인 (인증 위임)