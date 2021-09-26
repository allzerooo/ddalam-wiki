# AOP(Aspect Oriendted Programming)

### Aspect
여러 객체에 공통적으로 적용되는 관심 사항으로, 어디에서(pointcut) 무엇을 할 것인지(advice)를 합쳐놓은 개념

## Aspect 정의하기

### 방법 1 : 클래스에 @Aspect와 @Component 붙이기

### 방법 2 : 설정 클래스에 @EnableAspectJAutoProxy 붙이기

## pointcut

jointpoint를 찾는 표현식

## jointpoint

pointcut으로 매치한 실행 지점

## advice

jointpoint에서 해야할 일


### Aspect가 여러 개 적용되었을 때 우선순위 설정하기


## Spring AOP와 AspectJ
Spring AOP 프레임워크는 제한된 타입의 AspectJ 포인트컷만 지원하며 IoC 컨테이너에 선언한 빈에 한하여 애스펙트를 적용할 수 있다. 따라서 포인트컷 타입을 추가하거나, IoC 컨테이너 외부 객체에 애스펙트를 적용하려면 스프링 애플리케이션에서 AspectJ 프레임워크를 직접 끌어쓰는 수밖에 없다

## 주요 Annotation
- `@Aspect` : 자바에서 널리 사용하는 AOP 프레임워크에 포함되며, AOP를 정의하는 Class에 해당
- `@Pointcut` : 기능을 어디에 적용시킬지, 메서드? Annotation? 등 AOP를 적용 시킬 지점을 설정
- `@Before` : 메서드 실행하기 이전
- `@After` : 메서드가 성공적으로 실행 후, 예외가 발생 되더라도 실행
- `@AfterReturning` : 메서드 호출 성공 실행 시 (Not Throws)
- `@AfterThrowing` : 메서드 호출 실패 예외 발생 (Throws)
- `@Around` : Before / after 모두 제어