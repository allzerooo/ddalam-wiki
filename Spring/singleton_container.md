# 싱글톤 컨테이너

- 클라이언트가 서버에 동시에 요청을 할 때마다 객체를 생성하면 메모리 낭비가 심하다. 따라서 객체를 1개만 생성하고, 공유하도록 하는 싱글톤 패턴이 사용된다.

- 스프링 컨테이너는 스프링 빈을 싱글톤으로 관리한다

- 설정 정보를 사용해 스프링 컨테이너에 빈을 등록할 때 싱글톤을 보장하기 위해, 스프링은 `@Configure` 가 있는 클래스의 바이트코드를 조작하는 라이브러리를 사용해 새로운 클래스를 생성, 빈으로 등록한다. 이렇게 생성된 클래스는 스프링 빈이 존재하면 존재하는 빈을 반환하고, 스프링 빈이 없으면 빈을 생성하고 등록해 반환한다