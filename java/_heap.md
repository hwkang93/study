# Heap

JAVA 에서의 Heap 영역이란, 런타임 시 자바 객체와 배열이 할당되는 영역으로 동적 메모리 할당에 사용된다.
객체는 힙 공간에 생성되고 객체의 참조값은 스택 메모리에 저장된다.



Heap 에 있는 객체들에 대한 참조는 다음 4가지 종류 중 하나이다.
- Heap 내의 다른 객체에 의한 참조
- 스택에서의 참조 (Java 메소드 실행 시에 사용하는 지역 변수와 파라미터들에 의한 참조)
- Native Stack, 즉 JNI(Java Native Interface)에 의해 생성된 객체에 대한 참조
- 메서드 영역의 정적 변수에 의한 참조


## Heap 의 두 가지 대전제 (Weak Generational Hypothesis)

Heap 을 최초로 구현할 때 두 가지의 대전제를 바탕으로 구현하였다고 한다.

- 대부분의 객체는 금방 접근 불가능한 상태(Unreachable)가 된다.
- 오래된 객체에서 새로운 객체로의 참조는 아주 적게 존재한다.

**객체는 대부분 일회성이며, 메모리에 오랫동안 남아있는 경우는 드물다.**


## Heap 영역의 특징

- Heap 공간이 가득 차면 java.lang.OutOfMemoryError 를 발생시킨다.
- Heap 메모리에 대한 접근은 스택 메모리에 비해 느리다.
- GC 를 통해 참조되지 않은 객체의 메모리를 수집한다.
- Stack 과 다르게 쓰레드로부터 안전하지 않으며, 적절한 동기화를 통해 보호해야 한다.

## Heap 의 구조

힙(Heap)은 Young, Old 영역으로 구성되어 있다.

(초기에는 Perm 영역이 존재하였지만 Java 8 부터는 제거되었다.)


### Young 영역(Young Generation)
- 새롭게 생성된 객체가 할당(Allocation)되는 영역
- 대부분의 객체가 금방 접근 불가능한 상태(Unreachable)가 되기 때문에, 많은 객체가 Young 영역에 생성되었다 사라진다.
- Young 영역은 1개의 Eden 영역과 2개의 Survivor 영역으로 다시 구분된다. 
  - Eden 영역 : 새로 생성된 객체가 최초로 할당(Allocation)되는 영역
  - Survivor 영역 : 최소 1번의 GC 가 진행된 후 살아남은 객체가 존재하는 영역

  > HotSpot JVM 에서는 Eden 영역에 객체를 빠르게 할당하기 위해 **bump the pointer** 와 TLABs(Thread-Local Allocation Buffers) 라는 기술을 사용하고 있다.
  > 
  > **bump the pointer**
  > Eden 영역에 마지막으로 할당된 객체의 주소를 캐싱해두는 것.
  > 새로운 객체를 위해 유효한 메모리를 탐색할 필요 없이 마지막 주소의 다음 주소를 사용하게 함
  > 
  > **TLABs(Thread-Local Allocation Buffers)**
  > 각각의 쓰레드마다 Eden 영역에 객체를 할당하기 위한 주소를 부여함으로써 동기화 작업 없이 빠르게 메모리를 할당하도록 하는 기술


### Old 영역(Old Generation)
- Young 영역에서 살아남은 객체가 복사되는 영역
- 복사되는 과정에서 대부분 Young 영역보다 크게 할당된다.
- 512 bytes 의 덩어리(Chunk)로 되어있는 카드 테이블(Card Table)이 존재한다.
  - Old 영역의 객체가 Young 영역의 객체를 참조하는 경우를 대비
  - 카드 테이블에는 Old 영역의 객체가 Young 영역의 객체를 참조할 때마다 그에 대한 정보가 표시된다. 





## Reference

[baeldung - Stack Memory and Heap Space in Java](https://www.baeldung.com/java-stack-heap)

[망나니개발자 - [Java] Garbage Collection(가비지 컬렉션)의 개념 및 동작 원리 (1/2)](https://mangkyu.tistory.com/118)

[Naver D2 - Java Reference 와 GC](https://d2.naver.com/helloworld/329631)