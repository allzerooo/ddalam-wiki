# `SpringApplication` Class

`SpringApplication` 인스턴스를 만들어 `run()` 전에 각종 설정 가능

```java
public static void main(String[] args) {
    SpringApplication app = new SpringApplictaion(SpringPracticeApplication.class);
    app.setBannerMode(Banner.Mode.OFF);
    app.run(args);
}
```

<br/>

---

<br/>

출처 및 참고
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)
