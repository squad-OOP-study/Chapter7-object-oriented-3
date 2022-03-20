## 파서드 패턴

- 파사드는 내부 시스템의 복잡함을 숨기고 아주 단순화된 방식으로 시스템에 접근할 수 있는 인터페이스를 클라이언트에게 제공한다.

  우리가 가게에 가서 물건을 살 때, 그 가게 안의 진열대가 어떻게 생긴지 몰라도 직원에게 물건을 가져다 달라고 하면 바로 물건을 얻을 수 있다. 여기서 가게 직원이 인터페이스이다.
  
> 구조

- Façade

  복잡한 서브시스템 그룹을 묶어 바깥 세계에 간단하게 보여주는 것 
  
- System

  전체 시스템이 어떻게 작동하는지 알아보기 어렵게 만드는 다양한 서브시스템들의 모음

- Client

  클라이언트는 파사드와 상호작용해 시스템과 쉽게 소통 및 작업을 완수

  파사드를 인스턴스화하는 클래스




## 추상 팩토리 패턴

- 팩토리 메서드 패턴

  조건에 따른 객체 생성을 팩토리 클래스로 위임한다. 팩토리 클래스에서 객체를 생성한다.

- 추상 팩토리 패턴

  서로 관련이 있는 객체들을 묶어서 팩토리 클래스를 만든다. 팩토리를 조건에 따라 생성하도록 팩토리를 만들어서 객체를 생성한다.

- 비슷해보이지만 차이가 있기 때문에 적절한 선택이 필요하다.

> 장점

- 콘크리트 클래스를 사용자에게 분리된다

  사용자는 인터페이스에 대해 정보만 알고 있다.

- 추상 제품을 상속받는 객체를 대체하거나 교체할 수 있다.

> 단점

- 새로운 제품 클래스가 추가되면, 수정작업이 어려워진다.

- 추상 팩토리를 상속하고 있는 모든 팩토리에도 새로운 제품에 대한 구현 방법을 수정해야한다.


## 컴포지트 패턴

- 단일 객체와 그 객체들을 가지는 집합 객체를 같은 타입으로 취급해, 트리 구조로 객체들을 엮는 패턴이다.

- 객체들 간에 계급 및 계층구조가 있고 이를 표현해야 할 때 이용한다.

  클라이언트가 단일 객체와 집합 객체를 구분하지 않고 동일한 형태로 사용하고자 할 때 이용한다.

> 구조

- Component

  Leaf와 Composite가 구현해야하는 Interface로, Leaf와 Composite는 모두 Component라는 같은 타입으로 다뤄진다.

- Leaf

  단일 객체로 Composite의 부분(자식) 객체로 들어간다. 이 때, Component의 형태로 들어간다.

- Composite

  집합 객체로 Leaf 객체나 Composite를 부분(자식)으로 둔다. 이 때, Component의 형태로 들어간다.

  클라이언트는 이 Composite를 통해 부분 객체들을 다룰 수 있다.

> 장점

- 객체들이 모두 같은 타입으로 취급되서 클래스 추가가 용이하다.

- 단일, 집합객체 구분하지 않고 코드 작성이 가능하다.

> 단점

- 설계를 일반화시켜 객체 간의 구분, 제약이 힘들다.


## 널 객체 패턴

- 유저가 직접 널 체크를 하지 않고, 빈 객체를 제공함으로써 예외 처리를 피하는 패턴이다.

> 장점

- 클라이언트 코드에서 널 처리를 최소화 할 수 있다.

> 단점

- 널 객체를 요구하는 클래스마다 인터페이스를 선언하고 구현하는 널 객체를 생성해야 한다.

- 클라이언트에서 널 객체를 모르면, 디버깅 시 혼란을 가져온다.

- 널 검사가 굳이 필요 없는 곳에서 복잡한 구조를 가지게 된다.
