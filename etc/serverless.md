- [Serverless](#serverless)
  - [Backend as a Service (BaaS)](#backend-as-a-service-baas)
  - [Functions as a Service (Faas)](#functions-as-a-service-faas)

# Serverless

“서버의 존재”에 대해서 신경쓰지 않아도 된다

→ 서버가 어떤 사양으로 돌아가고있는지, 서버의 갯수를 늘려야 할지, 네트워크는 어떤걸 사용할지, 이런걸 설정할 필요가 없다


## Backend as a Service (BaaS)

서버 개발을 하다보면 고려해야 할 데이터베이스, 소셜 서비스 연동, 파일시스템 등을 API로 제공 → 서버 개발을 직접 하지 않아도 필요한 기능을 쉽고, 빠르게 구현할 수 있게 해주고, 서버 이용자가 늘어도 알아서 확장

- ex) Firebase
- 백엔드에 대한 지식이 별로 없더라도 빠르게 개발 가능
  - 예를 들어, Firebase에는 실시간 데이터베이스를 사용하여 클라이언트에게 바로 반영시켜주는 기능이 있는데 이러한 기능은 직접 개발하게 되면 꽤 많이 시간이 필요할수도 있다
- 소규모 프로젝트의 경우 유용하게 사용할 수 있다
- 단점
  - 백엔드 로직들이 클라이언트쪽에 많이 구현될 수 있다
  - 모바일 앱이라면 클라이언트 코드 수정으로 앱 업데이트를 해야한다
  - 복잡한 데이터베이스를 모델링하기 어려울 수 있다


## Functions as a Service (Faas) 

프로젝트를 여러개의 함수로 쪼개서 → 분산된 컴퓨팅 자원에 함수를 등록 → 서버 준비, 인프라 관리, 보안, 트래픽 대응 등을 알아서 해줌 (코드 작성에만 집중하면 된다)

- ex) AWS Lambda, Google Functions, Azure Functions
- 하나 하나의 함수를 자유롭게 배포할 수 있다
- endpoint 들을 분리해서 작은 부분들, 즉 마이크로서비스로 나눈다 → 실행된 함수를 기준으로 비용을 지불하게
- 함수가 호출될 때 최대 메모리, 최대 처리 시간에 제한이 있기 때문에 웹소켓 같이 계속 켜놔야 되는 서비스는 사용할 수 없음


<br/>

---

<br/>


출처

- [서버리스 아키텍쳐란?](https://velopert.com/3543)
- [[번역]빠르게 배워보는 Node.js를 이용한 서버리스(Serverless)](https://medium.com/@jwyeom63/%EB%B9%A0%EB%A5%B4%EA%B2%8C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EB%8A%94-node-js%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%84%9C%EB%B2%84%EB%A6%AC%EC%8A%A4-serverless-503ee61539d4)