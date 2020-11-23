# BeanFactory, ApplicationContext

### BeanFactory

- 스프링 컨테이너의 최상위 인터페이스
- 스프링 빈을 관리, 조회하는 역할

### ApplicationContext

- `BeanFactory` 기능을 모두 상속
- 애플리케이션 개발 시 빈 관리, 조회 외에 필요한 부가기능들을 제공
  - `MessageSource` : 메시지 소스를 활용한 국제화 기능
  - `EnvironmentCapable` : 환경변수
  - `ApplicationEventPublisher` : 애플리케이션 이벤트
  - `ResourceLoader` : 리소스 조회

<br/>

[출처]

- 인프런 스프링 핵심 원리 기본편 (김영한)
