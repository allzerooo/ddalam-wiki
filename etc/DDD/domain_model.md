
# 도메인 모델

## 도메인

소프트웨어로 해결하고자 하는 문제 영역



## 하위 도메인

도메인은 여러 하위 도메인으로 구성된다



## 도메인 모델 ( 개념 모델 )

도메인을 이해하는 데 도움을 주는 표현 방식

- 객체 모델링
- 상태 전이 모델링
- 등등

처음부터 완벽한 도메인 모델을 만드는 것은 불가능

구현하는 과정에서 개념 모델을 점진적으로 발전시켜 나가야 한다



## 도메인 모델 (패턴) ( 구현 모델 )

아키텍처 상의 도메인 계층을 객체 지향 기법으로 구현하는 패턴

### 도메인 계층

- 핵심 규칙(=도메인 규칙)을 구현
    - ex) 주문 도메인의 경우 ‘출고 전에 배송지를 변경할 수 있다’라는 규칙과 ‘주문 취소는 배송 전에만 할 수 있다’라는 규칙을 구현한 코드가 도메인 계층에 위치하게 된다



## 도메인 모델 도출

도메인을 모델링할 때 기본이 되는 작업은 모델을 구성하는 핵심 구성요소, 구칙, 기능을 찾는 것이다

이 과정은 요구사항에서 출발한다

ex) 주문 도메인 요구사항

- 최소 한 종류 이상의 상품을 주문해야 한다 → 주문 항목이 어떤 데이터로 구성되는지 알려준다
- 출고를 하면 배송지를 변경할 수 없다 → ‘출고 상태로 변경하기’라는 기능을 메서드로 추가할 수 있다

요구사항에서 메서드 도출

요구사항에서 객체가 가져야 할 필드 도출

요구사항에서 객체간 관계 도출

요구사항에서 제약조건 도출



도메인 모델의 도출 결과로 엔티티와 밸류 타입을 얻을 수 있다



## 엔티티

엔티티 객체마다 고유한 식별자를 갖는다

따라서 두 엔티티 객체의 식별자가 갖으면 두 엔티티를 갖다고 판단하는 `equals()` 와 `hashCode()` 를 구현할 수 있다

### 엔티티 식별자 생성

식별자 생성 시점은 도메인의 특징과 사용하는 기술에 따라 달라진다

흔히 다음 중 한 가지 방식으로 생성한다

- 특정 규칙에 따라 생성 → 주문번호, 운송장번호, 카드번호 등
- UUID나 Nano Id와 같은 고유 식별자 생성기 사용 → 다수의 언어가 UUID 생성기를 제공
- 값을 직접 입력 → 회원의 아이디, 이메일
- 일련번호 사용 → 시퀀스나 DB의 자동 증가 컬럼 사용



## 밸류 타입

개념적으로 완전한 하나를 표현할 때 사용

```java
// '받는 사람'이라는 도메인 개념
public class Receiver {
  private String name;
  private String phoneNumber;
  
  public Receiver(String name, String phoneNumber) {
    this.name = name;
    this.phoneNumber = phoneNumber;
  }
  
  public String getName() {
    return name; 
  }
  
  public String getPhoneNumber() {
    return phoneNumber;
  }
  
  // Receiver 밸류 타입을 위한 기능들을 추가
}
```

밸류 객체의 데이터를 변경할 때는 기존 데이터를 변경하기보다는 변경한 데이터를 갖는 새로운 밸류 객체를 생성하는 방식을 선호한다 → 변경을 막아서 안전한 코드를 작성할 수 있기 때문에

엔티티의 식별자는 도메인에서 특별한 의미를 지니는 경우가 많기 때문에 식별자를 위한 밸류 타입을 사용해서 의미가 잘 드러나도록 할 수 있다 ( String 보다 OrderNo 밸류 타입이 주문번호라는 식별자의 의미를 더 잘 나타낸다 )



도메인 모델에 습관적으로 get/set을 사용하지 말고

- 도메인 모델과 관련된 도메인 지식을 코드로 구현하는 것이 좋다
- 객체가 불완전한 상태로 사용되는 것을 막으려면 생성자를 통해 필요한 데이터를 모두 받아 생성 시점에 데이터가 올바른지 검사할 수 있다
- set 메서드를 구현해야 할 특별한 이유가 없다면 밸류 타입은 불변으로 구현해 불변 타입의 장점을 살린다



## 유비쿼터스 언어

전문가, 관계자, 개발자가 도메인과 관련된 공통의 언어를 만들고 이를 대화, 문서, 도메인 모델, 코드, 테스트 등 모든 곳에서 같은 용어를 사용

시간이 지날수록 도메인에 대한 이해가 높아지는데 새롭게 이해한 내용을 잘 표현할 수 있는 용어를 찾아내고 이를 다시 공통의 언어로 만들어 다 같이 사용

도메인 용어에 알맞은 단어를 찾는 노력을 해야 한다 → 도메인 용어의 ‘상태’를 코드로 표현할 때 ‘state’와 ‘status’ 중 어떤 단어를 사용할지 고민하는 것과 같은

UUID : Universally Unique Identifier

<br/>

---

<br/>

참고 및 출처
- 도메인 주도 개발 시작하기 DDD 핵심 개념 정리부터 구현까지 - 최범균