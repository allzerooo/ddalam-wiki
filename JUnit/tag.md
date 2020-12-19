# 태그

`@Tag`를 사용

<br/>

- 테스트를 그룹화하고 원하는 그룹만 테스트를 실행할 수 있다
  ```
  예를 들어,
  로컬에서만 테스트하고 싶은 메서드에는 `@Tag("local")`을 추가하고, CI 빌드 중 실행해야될 테스트 메서드에는 `@Tag("CI")`를 추가한다. 그리고 원하는 태그 그룹만 테스트하도록 필터링을 적용한다
  ```
- 하나의 테스트 메서드에 여러 태그를 사용할 수 있다

<br/>

### 원하는 테스트 그룹만 실행하는 방법

<b>IntelliJ</b><br/>
Edit configurations - JUnit - Configuration - Test kind 선택

- class : 해당 클래스의 모든 테스트를 실행
- tags : 선택한 태그가 있는 테스트만 실행

<br/>
<b>pom.xml</b><br/>

```xml
<profiles>
   <profile>
        <id>default</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <build>
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <groups>fast</groups>
                </configuration>
            </plugin>
        </build>
    </profile>
    <profile>
        <id>ci</id>
        <build>
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <groups>fast | slow</groups>
                </configuration>
            </plugin>
        </build>
    </profile>
</profiles>
```

```bash
# ci 로 빌드
./mvnw test -P ci
```

<br/>

### 커스텀 태그

JUnit5가 제공하는 애노테이션은 메타 에노테이션으로 사용할 수 있다. 따라서 커스텀 태그 애노테이션도 만들 수 있다

<br/>

---

<br/>

[출처]

- 인프런 더 자바 코드를 테스트하는 다양한 방법 (백기선)
