- [Spring Security](#spring-security)
  - [Authentication(인증), Authorization(인가)](#authentication인증-authorization인가)
    - [Authentication](#authentication)
      - [다양한 인증 방법](#다양한-인증-방법)
    - [Authorization](#authorization)

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