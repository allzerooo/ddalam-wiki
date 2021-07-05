# 프록시(Proxy)

서버와 클라이언트의 양쪽 역할을 하는 중계 프로그램으로, 클라이언트로부터의 리퀘스트를 서버에 전송하고, 서버로부터의 리스폰스를 클라이언트에 전송한다.

프록시는 forward proxy, reverse proxy 등 여러가지가 존재한다. 

<br/>

### reverse proxy
서버 요청에 대해 서버 앞 단에 존재하면서 서버로 들어오는 요청을 대신 받아서 서버에 전달하고, 요청한 곳에 결과를 다시 전달하는 역할을 한다.

<br/>

---

참고
- 그림으로 배우는 Http & Network Basic (우에노 센)
- L4/L7 스위치의 대안, 오픈 소스 로드 밸런서 HAProxy(https://d2.naver.com/helloworld/284659)