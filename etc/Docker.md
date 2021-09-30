# Docker

- [Docker](#docker)
  - [Docker란?](#docker란)
    - [docker가 environment disparity 문제를 해결하는 방법](#docker가-environment-disparity-문제를-해결하는-방법)

## Docker란?

docker는 environment disparity라는 문제점을 해결해준다

**environment disparity**

로컬에서 개발을 하고 배포하려고 코드를 서버에 올렸더니 작동을 안하네? 로컬 컴퓨터와 서버 컴퓨터의 환경(윈도우냐 리눅스냐와 같은)이 다르기 때문에 생기는 문제

→ docker를 통해 서로 다른 머신에서도 같은 환경을 구현할 수 있다

### docker가 environment disparity 문제를 해결하는 방법
예를 들어, 도커를 내 컴퓨터에도 설치하고 서버 컴퓨터에도 설치를 한다 → 그리고 Dockerfile이라는 것을 생성한다 → 여기에 구현하고 싶은 환경을 설정하면 된다(우분투, 파이썬, 깃 등등) → 이 파일을 내 컴퓨터, 서버 컴퓨터에 두면 → 도커는 이 파일을 읽고 필요한걸 다 다운로드 받아서 → 파일에 설정된 환경과 같은 Virtual Container(우분투, 파이썬, 깃 등등이 설치된)를 내 컴퓨터와 서버 컴퓨터에 동일하게 만든다 → 내 컴퓨터와 서버 컴퓨터에 동일한 환경을 구성했기 때문에 내 컴퓨터에서 서버 컴퓨터로 코드를 업로드 할 떄 잘 작동할 것이다!

도커 컨테이너들은 모두 분리, 독립되어 있다. 이 특징 덕분에 한 개의 서버에 각기 다른 많은 수의 컨테이너를 가질 수 있다. 예를 들어, 어떤 컨테이너는 node.js 컨테이너, 또 다른 컨테이너는 Java 컨테이너, Python 컨테이너와 같이. 그렇기 때문에 갑자기 Java 애플리케이션의 트래픽이 증가하면 동일한 Java 컨테이너의 수를 늘렸다가, 트래픽이 줄면 컨테이너를 줄일 수 있다. 매번 새로운 서버를 만들때마다 새로운 서버를 사고, 설정할 필요가 없는 것이다 → **그냥 컨테이너를 생성하고 원하는 수만큼 복제하면 된다**


<p align="center">
    <img src="../image/docker_container.png"  width="700" height="auto">
</p>

하나의 같은 서버에 각기 다른 환경의 컨테이너를 설정할 수 있고, 각 컨테이너들은 독립되어 있다.



<br/>

---

<br/>

참고 및 출처
- [노마드코더 유튜브](https://www.youtube.com/watch?v=chnCcGCTyBg)