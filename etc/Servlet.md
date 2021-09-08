# 서블릿

예를 들어 웹 브라우저에서 요청이 발생하면, 웹 브라우저는 HTTP 메시지를 만들어 서버로 요청 메시지를 전송한다.

<p align="center">
    <img src="../image/http_message.png"  width="480" height="auto">
</p>

만약, WAS를 직접 구현한다면

<p align="center">
    <img src="../image/WAS_function.png"  width="300" height="auto">
</p>

모든 요청에 공통으로 필요한 기능(위 이미지에서 녹색 테두리가 쳐진 부분을 제외한) + 비즈니스 로직을 매번 구현해야된다. 서블릿은 모든 요청에 공통으로 필요한 기능을 개발자가 직접 구현하지 않도록 지원하는 **WAS의 Java 프로그램**이다. 따라서, 서블릿을 지원하는 WAS를 사용하면 비즈니스 로직 부분을 제외한 부분은 WAS가 처리를 해준다.

서블릿은 Java 클래스의 일종으로, 특정 요청을 처리하기 위해서는 서블릿이 요구하는 구현 규칙만 지켜 구현하면 된다.

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) {
        // 애플리케이션 로직
    }
}
```
위 코드는 `urlPatterns`(/hello)의 URL이 호출되었을 때 실행되는 서블릿 코드이다. 


<p align="center">
    <img src="../image/http_request_WAS.png"  width="560" height="auto">
</p>

WAS는 HTTP 요청을 받으면 HTTP 요청 메시지를 기반으로 `HttpServletRequest`와 `HttpServletResponse` 객체를 생성한다. `HttpServletRequest`는 HTTP 요청 정보를 편리하게 사용할 수 있도록 지원하며, `HttpServletResponse`는 HTTP 응답 정보를 편리하게 제공할 수 있도록 지원한다. 이를 통해 개발자는 HTTP 스펙을 매우 편리하게 사용할 수 있다.

WAS는 Request, Response 객체를 새로 만들어 서블릿 객체를 호출한다(Request, Response 객체를 `helloServlet`의 `service()` 메서드 파라미터로 전달). 서블릿 코드가 종료되면 WAS는 Response 객체에 담겨있는 내용으로 HTTP 응답 정보를 생성한다.

### 서블릿 컨테이너

<p align="center">
    <img src="../image/servlet_container.png"  width=520" height="auto">
</p>

- 톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 한다. 서블릿은 개발자가 직접 생성하고, 호출하는게 아니라 서블릿 컨테이너가 생성, 초기화, 호출, 종료하는 생명주기를 관리한다.

- 서블릿 컨테이너는 서블릿 객체를 싱글톤으로 관리한다. HTTP 요청은 모두 다른 것이기 때문에 `HttpServletRequest`와 `HttpServletResponse`는 각 요청마다 생성되지만 서블릿은 모든 요청이 동일하게 사용하는 코드이기 때문에 요청이 올 때마다 서블릿 객체를 생성하는 것은 비효율적이기 때문이다. 따라서 모든 요청은 동일한 서블릿 객체 인스턴스에 접근하게 되고, 공용 변수 사용에 주의해야 한다. 

- 서블릿 컨테이너는 JSP도 서블릿으로 변환 되어서 사용한다.

- 서블릿 컨테이너는 동시 요청을 위한 멀티 쓰레드 처리를 지원한다. 따라서 천명, 만명의 동시 요청을 개발자가 신경쓰지 않아도 되는 것이다.




<br/>

---

<br/>

출처 및 참고

- [스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)
- [자바 서블릿](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EC%84%9C%EB%B8%94%EB%A6%BF)
- [서블릿이란](https://ecsimsw.tistory.com/entry/%EC%84%9C%EB%B8%94%EB%A6%BF%EC%9D%B4%EB%9E%80?category=915995)