# `@Configuration`

애플리케이션의 전체 동작 방식을 구성하기 위해 구현 객체를 생성하고, 연결하는 역할을 한다.

<a href="https://github.com/ddalam/ddalam-wiki/blob/master/Spring/component_scan.md#component-scan">컴포넌트 스캔</a>으로 `@Configuration`을 찾아, Bean 설정(메서드)을 읽어서 스프링 컨테이너에 등록한다. 

각종 스프링 인터페이스의 구현에도 활용된다.
```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Override
    protected void configure(HttpSecurity http) {
        ...
    }
}
```
```java
@Configuration
@EnableWebMvc
public class Config implements WebMvcConfigurer {
    
    @Override
    public void addFormatters(FormatterRegistry registry) {
        ...
    }
}
```


<br/>

---

<br/>

출처 및 참고
- 인프런 - 스프링 핵심 원리 기본편 (김영한)
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)