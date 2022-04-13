# MVC 패턴

## Controller Layer - 애노테이션 기반 설계

### 컨트롤러 클래스
핸들러 메서드를 포함하는 컨트롤러 빈을 만드는 과정
- `@Controller` : 클래스 안에 있는 메서드를 뷰와 연결시킬 수 있다
- `@RestController` = `@ResponseBody` + `@Controller`
  - `@ResponseBody` : 뷰를 찾는게 아니라 핸들러 메서드의 출력 결과를 그대로 내보내준다
  
#### 핸들러 메서드 (Handler Method)
- Spring Web에서 사용자의 요청(request)을 받아 응답(response)을 리턴하는 메서드
- 스프링 웹 서비스가 받은 URI 요청을 컨트롤러 클래스의 특정 메서드에 매핑하는 과정
    ```java
    @GetMapping("/places/{placeId}")
    public String getPlace(@PathVariable Integer placeId) {
        return "place" + placeId;
    }
    ```
    - `/places/{placeId}` : 매핑 정보
    - `@PathVariable Integer placeId` : 요청
    - `return "place" + placeId` : 응답
- `@RequestMapping` : 컨트롤러 클래스 메서드에 공통으로 들어가는 URI를 클래스 레벨에서 지정할 수 있음, 메서드 레벨에서도 사용 가능
  - name : 뷰 템플릿에서 식별할 때 쓰는 이름
  - value, path : URI
  - method : HTTP method (ex: GET, POST, ...)
  - params : 쿼리 파라미터 검사
  - headers : 헤더 검사
  - consumes : 헤더의 Content-Type 검사
  - produces : 헤더의 Accept 검사
  - shortcuts
    - `@GetMapping`
    - `@PostMapping`
    - `@PutMapping`
    - `@DeleteMapping`
    - `@PatchMapping`
- 받을 수 있는 요청들(메서드 파라미터로 적어 넣을 수 있는 타입들)
  - ServletRequest, SEvletResponse, HttpSession
  - WebRequest, NativeWebReqeust
  - @RequestParam, @PathVariable
    - @RequestParam 생략하면 required = false로 적용됨
  - @RequestBody, HttpEntity<B>
  - @ModelAttribute, @SettionAttribute, Model, ModelMap
  - @RequestHeader, @CookieValue
  - Principal, Local, TimeZone, InputStream, OutputStream, Reader, Writer, ...
  - 이 외 많음
- 내보낼 수 있는 응답들(메서드가 리턴할 수 있는 타입들)
  - ModelAndView
  - String, View
  - @ModelAttribute, Map, Model
  - @ReponseBody
  - HttpEntity<B>, ReponseEntity<B>
  - HttpHeaders
  - void
  - 등등
  
#### URI 매핑
- `@PathVariable`

## Controller Layer - 함수 기반 설계

### 함수형 엔드포인트
Spring Web의 엔드포인트를 함수형 스타일로 작성하는 방법을 제공
- WebMvc.fn
- routing, request handling
- 불변성을 고려하여 설계됨
- 기존의 DispatcherServlet 위에서 동작
- 애노테이션 스타일과 함께 사용 가능

### 주요 키워드
- HandlerFunction == @RequestMapping
  - 입력 : ServerReqeust
  - 출력 : ServerResponse
  - 결과 : data
- RouterFunction == @RequestMapping
  - 입력 : ServerReqeust
  - 출력 : Optional<HandlerFunction>
  - 결과 : data + behavior(ex: url mapping)

### 기타 세부 키워드
- RequestPredicates
- RouterFunctions.route().nest()
- RouterFunctions.route().before()
- RouterFunctions.route().after()
- RouterFunctions.Builder.onError()
- RouterFunctions.Builder.filter()


<br/>

---

<br/>

출처 및 참고
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)