# Heap

Heap 에 있는 객체들에 대한 참조는 다음 4가지 종류 중 하나이다.
- Heap 내의 다른 객체에 의한 참조
- 스택에서의 참조 (Java 메소드 실행 시에 사용하는 지역 변수와 파라미터들에 의한 참조)
- Native Stack, 즉 JNI(Java Native Interface)에 의해 생성된 객체에 대한 참조
- 메서드 영역의 정적 변수에 의한 참조




## Heap 의 두 가지 대전제 (Weak Generational Hypothesis)
- 대부분의 객체는 금방 접근 불가능한 상태(Unreachable)가 된다.
- 오래된 객체에서 새로운 객체로의 참조는 아주 적게 존재한다.

**객체는 대부분 일회성이며, 메모리에 오랫동안 남아있는 경우는 드물다.**




## Heap 의 구조

힙(Heap)은 Young, Old 영역으로 설계되었다.

(초기에는 Perm 영역이 존재하였지만 Java 8 부터는 제거되었다.)


### Young 영역(Young Generation)
- 새롭게 생성된 객체가 할당(Allocation)되는 영역
- 대부분의 객체가 금방 접근 불가능한 상태(Unreachable)가 되기 때문에, 많은 객체가 Young 영역에 생성되었다 사라진다.
- Young 영역은 1개의 Eden 영역과 2개의 Survivor 영역으로 다시 구분된다. 
  - Eden 영역 : 새로 생성된 객체가 최초로 할당(Allocation)되는 영역
  - Survivor 영역 : 최소 1번의 GC 가 진행된 후 살아남은 객체가 존재하는 영역



### Old 영역(Old Generation)
- Young 영역에서 살아남은 객체가 복사되는 영역
- 복사되는 과정에서 대부분 Young 영역보다 크게 할당된다.
- 512 bytes 의 덩어리(Chunk)로 되어있는 카드 테이블(Card Table)이 존재한다.
  - Old 영역의 객체가 Young 영역의 객체를 참조하는 경우를 대비
  - 카드 테이블에는 Old 영역의 객체가 Young 영역의 객체를 참조할 때마다 그에 대한 정보가 표시된다. 





## Reference

[망나니개발자-[Java] Garbage Collection(가비지 컬렉션)의 개념 및 동작 원리 (1/2)](https://mangkyu.tistory.com/118)
[Naver D2 - Java Reference 와 GC](https://d2.naver.com/helloworld/329631)