- [AOP(Aspect Oriendted Programming)](#aopaspect-oriendted-programming)
  - [AOP가 필요한 상황](#aop가-필요한-상황)
  - [AOP란?](#aop란)
  - [AOP를 적용하지 않으면?](#aop를-적용하지-않으면)
  - [AOP를 적용하면?](#aop를-적용하면)
  - [AOP를 적용했을 때 의존관계](#aop를-적용했을-때-의존관계)
  - [`@Aspect`](#aspect)
  - [포인트컷](#포인트컷)
  - [조인 포인트](#조인-포인트)

# AOP(Aspect Oriendted Programming)

## AOP가 필요한 상황
핵심 관심 사항(core concern)이 아닌, 모든 메서드의 호출 시간을 측정하는 것과 같은 공통 관심 사항(cross-cutting concern)을 처리해야 할 때 필요하다

## AOP란?
관점 지향 프로그래밍 Aspect Oriented Programming

## AOP를 적용하지 않으면?
- 핵심 비즈니스 로직에 공통 관심 사항에 대한 로직이 섞여 유지보수가 어렵다
- 공통 관심 사항에 대한 로직을 변경할 때 모든 로직을 찾아가며 변경해야 한다
- 별도의 로직으로 분리하는 것도 어렵고, 분리를 해도 핵심 비즈니스 로직에 호출하는 로직이 추가되어야 한다

## AOP를 적용하면?
- 핵심 관심 사항과 시간을 측정하는 공통 관심 사항을 분리할 수 있다
- 공통 관심 사항을 별도의 공통 로직으로 만들 수 있다
- 핵심 관심 사항을 깔끔하게 유지할 수 있다
- 공통 관심 사항을 원하는 적용 대상을 선택할 수 있다

## AOP를 적용했을 때 의존관계
<p align="center">
    <img src="../image/spring_aop_proxy.png"  width="480" height="auto">
</p>

## `@Aspect`
- AOP를 가능하게 하는 AspectJ 프로젝트에서 제공하는 애노테이션
- 스프링은 이것을 차용해서 프록시를 통한 AOP를 가능하게 한다
- 이 애노테이션을 사용하면 직접 AOP를 위한 프록시를 만들지 않아도 되도록 스프링이 편리하게 프록시를 만들어준다

## 포인트컷
- 표현식은 AspectJ 표현식을 사용한다

## 조인 포인트
- 실제 호출 대상, 전달 인자, 어떤 개체와 어떤 메서드가 호출되었는지에 대한 정보가 포함되어 있다