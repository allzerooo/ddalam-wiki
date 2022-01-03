- [Spring Native](#spring-native)
  - [목표](#목표)
    - [GraalVM](#graalvm)
    - [AOT (ahead-of-time compile)](#aot-ahead-of-time-compile)
    - [Native Image](#native-image)
  - [Spring Native로 할 수 있는 일](#spring-native로-할-수-있는-일)
  - [사용법](#사용법)

# Spring Native

## 목표
- GraalVM
- AOT
- High performance
- Reduced memory consumption

### GraalVM
"Run Programs Faster Anywhere"
- JVM의 일종
- 기존 C++로 만든 Hotspot JVM의 개발 한계를 극복하기 위한 Meta-circular JVM
  - Java로 만든 JVM
  - Meta-circular : 해당 언어의 컴파일러를 그 언어로 만든 것
- 성능, 클라우드 환경, 다양성을 고려
- [AOT](#aot-ahead-of-time-compile) 방식을 사용해 [Native Image](#native-image) 빌드

### AOT (ahead-of-time compile)
"미리 기계어로 번역한다"
- vs JIT(just-in-time) - 기계어 번역 시점이 언제인가?
  - JIT : 중간 언어(바이트코드) → 기계어(runtime)
  - AOT : 중간 언어(바이트코드) → 기계어(compile time)
- vs Static Compiler - 무엇을 기계어로 번역하는가?
  - Static Compiler : 소스 코드 → 기계어(compile time)
  - AOT : 중간 언어(바이트코드) → 기계어(compile time)
- 이점
  - 기계어 번역이 끝나 있으므로 속도가 더 빠름
  - 런타임에서 컴파일러를 필요로 하지 않아 더 가벼움

### Native Image
- AOT compiler를 이용해 Native Image 빌드
- 정적 분석 과정을 포함
- 네이티브 바이너리 결과물은 즉시 실행 가능한 기계 코드 전체를 포함 - JVM 불필요
- 다른 네이티브 이미지와 링크 가능
- 더 빠른 성능, 적은 메모리 소모
- 클라우드 네이티브 애플리케이션 배포에 효과적일 것으로 기대

## Spring Native로 할 수 있는 일
- lightweight docker container containing a native executable (by default)
- a native executable (using maven plugin)

## 사용법
- sdkman
  ```bash
  curl -s "https://get.sdkman.io" | bash
  source "~/.sdkman/bin
  ```

<br/>

---

<br/>

출처 및 참고
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)