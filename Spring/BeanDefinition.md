# BeanDefinitaion

스프링 빈 설정 메타 정보

### xml을 이용한 설정, Java 코드를 이용한 설정 등, 다양한 설정 형식 지원이 가능한 이유

스프링 컨테이너 입장에서는 xml을 이용한 설정인지, Java 코드를 이용한 설정인지는 몰라도 되고, 설정 정보를 읽어서 만든 `BeanDefinition`만 알면 되도록 설계되었기 때문 (`BeanDefinition` 인터페이스 즉, 추상화에만 의존하도록 설계)

<br/>

- 설정 정보에 있는 Bean 마다 메타 정보가 생성

<br/>

### 설정 정보를 사용해 `BeanDefinition`을 생성하는 방법

예를 들어, Java 코드를 사용해 설정 정보를 생성한다고 할 때

`AnnotationConfigApplicationContext`는 `AnnotatedBeanDefinitionReader`를 사용해서 `XXX.class`를 읽고 `BeanDefinition` 을 생성
