# DTO, VO, Entity 에 관하여

J2EE PATTERNS 라는 책의 초판에서 getter/setter 메소드를 가지고 데이터 전송을 위해 사용되는 객체를 VO 라고 명시는데,
2판에서는 초판에서 명시했던 VO 를 TO (Transfer Object) 라고 변경하였다.
그래서 VO 와 TO 는 혼용해서 사용되기 시작했고, 지금까지도 혼용된 상태로 사용되어지고 있다.

각 객체들은 무엇이고 어떠한 특징과 목적을 가지고 있는지 정리해보자.

### DTO (Data Transfer Object)

- 계층 간의 데이터 이동을 목적으로 하는 객체 
- getter/setter 메소드를 포함할 수 있다.
- 로직을 갖지 않는 순수한 객체를 의미한다.

### VO (Value Object)

- 값 그 자체를 표현하는 객체
- 객체의 불변성을 보장하여야 한다.
- 모든 속성값이 같은 VO 객체는 같은 VO 라고 판단한다.
- 그렇기 때문에 equals , hashcode 의 재정의가 필요하다.
> 객체를 새로 만들 때마다 서로 다른 메모리 주소를 가지기 때문에 객체의 속성값이 모두 같더라도 다른 객체로 판단한다.
> VO 의 특성상 모든 속성이 같은 VO 는 같은 VO 로 판단해야 하므로 재정의가 필요하다.

### Entity

- DB 테이블과 매핑되는 클래스
- 가변객체로, 객체 생성 후 상태를 변경할 수 있다.
- 로직을 포함할 수 있다.

## Reference

[[10분 테코톡] 라흐의 DTO vs VO - 우아한Tech Youtube](https://www.youtube.com/watch?v=J_Dr6R0Ov8E)