- [Spring Cloud Vault](#spring-cloud-vault)
  - [암호를 관리하는 방법](#암호를-관리하는-방법)
    - [암호를 어디에 관리할까?](#암호를-어디에-관리할까)
    - [Hashicorp Vault](#hashicorp-vault)
      - [장점](#장점)
      - [단점](#단점)
  - [Spring Vault](#spring-vault)
  - [Spring Cloud Vault](#spring-cloud-vault-1)
  - [설정 방법](#설정-방법)

# Spring Cloud Vault

## 암호를 관리하는 방법

### 암호를 어디에 관리할까?
- 코드에 직접 입력한다
- properties에 입력한다
- properties에 암호화하여 입력한다
- 별도 DB에 저장한다
- 배포 서버에 저장한다

### Hashicorp Vault
민감 정보 관리에 사용하는 오픈소스 도구

- 민감 정보의 저장, 관리
- 민감 정보에 접근하는 인증/권한 관리
- 데이터 암호화

Vault 서버를 따로 띄워 비즈니스 도메인을 처리하는 시스템과 민감 정보를 관리하는 시스템을 분리

#### 장점
- 프로젝트와 민감 정보가 완전히 분리됨
- 보안성 강화
- 민감 정보에 접근하고 고객과 공유할 수 있는 다양한 방법을 사용할 수 있음
- 민감 정보에 접근할 수 있는 권한 관리 기능

#### 단점
- 설계에 따라, Vault 서버가 죽으면 인증이 안되어 서비스가 중단되는 문제 발생(SPoF)
- 초기 러닝 커브
- Vault 서버를 별도 운영해야 함

## Spring Vault
- Vault 연동을 위한 기본 기능 지원
- package : spring-vault-core

## Spring Cloud Vault
- Vault가 외부 환경(클라우드)에 있는 경우를 위한 추가적인 지원
- Spring Vault를 포함
- Vault의 각종 설정을 properties 기반으로 조작 가능
- package : spring-cloud-starter-vault-config
- Vault에 등록한 Secret Data(key, value)를 Spring Properties에 등록한다
  - 예를 들어, Vault의 Secret Data에 key는 `spring.datasource.username`, value는 `XX`로 등록하면 우선순위에 의해서 application.properties를 먼저 읽고, 그 뒤에 Vault의 값으로 덮어씌워지기 때문에 Vault의 값으로 민감 정보를 변경할 수 있다

## 설정 방법
1. 의존성 추가
2. Vault 서버 띄우기, Vault UI를 사용해서 원하는 secret을 설정
3. properties 설정
   ```yml
   spring:
    config:
      import: vault://
    application:
      name: something
    cloud:
      vault:
        scheme: http
        authentication: token
        token: tokentokentoken
   ```
