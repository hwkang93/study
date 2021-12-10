> 현재 운영 중인 시스템의 메모리 관리가 정상적으로 되고 있는 것 같지 않다.
> 메모리 문제를 해결하기 위해서는 결국 GC 에 대해 알고 있어야 해결이 가능하다고 판단했고
> 정리할 필요가 있음을 느꼈다.
> 
> (21-12-10 추가)
> 정리를 하면 할수록 왜 GC 를 알아야 JAVA 를 제대로 안다는 이야기가 나왔는지 알게 되었다.
> 아무리 글들을 읽고 정리를 해봐도 스스로가 GC 에 대해 이해했다고 말하기가 불가능했다.
> 양이 많고 내용이 어려워 한 번에 이해하기 보다는 시간을 두고 조금씩 이해해가야겠다.

# Garbage Collection

Java GC (Garbage Collection) 는 Heap 메모리 내의 객체 중에서 Garbage 를 찾아 메모리를 회수하는 작업을 수행하는 시스템(? 소포트웨어?) 이다.

GC(Garbage Collection) 는 여러 구현 방식이 있지만, 모든 구현은 공통적으로 다음 2가지의 작업을 수행한다.

1. Heap 메모리 내의 객체 중에서 Garbage 를 찾아낸다.
2. 찾아낸 Garbage 를 처리해서 Heap 메모리를 회수한다.


## GC 가 Garbage 를 판단하는 방법

GC 는 기본적으로 Heap 내의 객체 중에서, Root Set 에서 참조하고 있지 않는 객체를 Garbage 로 판단한다.
하지만 좀 더 다양한 방법으로 객체를 처리하려는 요구가 있었고, 이에 따라 JDK 1.2 버전 부터는 java.lang.ref 패키지를 통해
제한적이나마 사용자 코드와 GC 가 상호작용 할 수 있는 기능을 제공하고 있다.

java.lang.ref 패키지는 Strong Reference (일반적인 코드로 메모리에 할당하는 객체) 외에도, Soft, Weak, Phantom 3가지의 참조방식을  각각의 Reference 클래스로 제공한다. 
이 Reference 클래스는 일정 부분 GC 에 관여할 수 있고, LRU(Least Recently Used) 캐시 같이 특별한 작업을 하는 애플리케이션을 더 쉽게 작석할 수 있다.

> **Root Set**
> 한 객체는 여러 객체를 참조하고, 참조된 여러 객체들도 마찬가지로 또다른 객체들을 참조하는 참조 사슬을 이루고 있다.
> 이런 상황에서 유효한 참조 여부를 판단하려면 항상 유효한 최초의 참조가 있어야 하는데 이를 객체 참조의 **Root Set** 이라고 한다.
>
> Root Set 은
> 1. Stack 영역에서의 참조 (지역 변수와 파라미터들에 의한 참조)
> 2. Native Stack 영역에서의 참조 (JNI(Java Native Interface) 에 의해 생성된 객체에 대한 참조)
> 3. 메소드 영역의 정적 변수에 의한 참조
>
> 가 있으며, Reachability 를 결정하는 기준이 된다.

### Reachability

직역하면 도달 가능성. Java GC 는 객체가 Garbage 인지 판별하기 위해서 Reachability 라는 개념을 사용한다.
GC 는 어떤 객체가 Root Set 에서 시작한 참조사슬에 속해있으면 Reachable 객체로 판단하고,
어떤 객체가 Root Set 에서 시작한 참조사슬에 속해있지 않으면 Unreachable 객체로 판단한다.

기본적으로 GC 는 Unreachable 객체를 Garbage 로 간주하고 메모리를 회수한다.

Reachable 은 다시 네 가지로 세분화 할 수 있다.
- Strongly Reachable : Root Set 으로부터 시작해 어떤 Reference Object 도 중간에 끼지 않은 상태로 참조 가능한 객체 (일반적인 객체들을 의미)
- Softly Reachable : Strongly Reachable 객체가 아니면서 Weak Reference, Phantom Reference 없이 Soft Reference 만 통과하는 참조 사슬이 하나라도 있는 객체
- Weakly Reachable : Strongly Reachable 객체, Softly Reachable 객체가 아닌 객체 중에서 Phantom Reference 없이 Weak Reference 만 통과하는 참조 사슬이 하나라도 있는 객체
- Phantomly Reachable : Strongly Reachable 객체, Softly Reachable 객체, Weakly Reachable 객체에 모두 해당하지 않는 객체. 이 객체는 Finalize 되었지만, 아직 메모리가 회수되지 않은 상태이다.
    > **Finalize** : GC 대상 객체를 처리하는 작업






 




## Garbage Collection 구현 방법

Garbage Collection 는 5가지 방법으로 구현이 가능하다.
- Serial Garbage Collector
- Parallel Garbage Collector
- CMS Garbage Collector
- G1 Garbage Collector
- Z Garbage Collector

하나씩 정리 해보자.

### Serial Garbage Collector

> java -XX:+UseSerialGC -jar Application.java

Serial GC 는 서버의 CPU 코어가 1개일 때 사용하기 위해 개발되었으며, 모든 GC 를 수행하기 위해 한 개의 쓰레드 만을 사용한다.
Serial GC 가 실행될 때 모든 어플리케이션 스레드를 정지시킨다.
그렇기 때문에 CPU 코어가 여러 개인 다중 스레드 프로그램에서는 Serial GC 를 사용하는 것은 지양해야 한다.

### Parallel Garbage Collector


### CMS Garbage Collector


### G1 Garbage Collector


### Z Garbage Collector

ZGC(Z Garbage Collector) 는 확장 가능한 저지연 GC(Scalable Low-latency Garbage Collector)이다.


## GC 가 객체를 처리하는 순서

GC 가 객체를 처리하는 순서는 항상 다음과 같다.

1. soft references
2. weak references
3. finalize
4. phantom references
5. 메모리 회수



## Reference
[망나니개발자-[Java] 다양한 종류의 Garbage Collection 알고리즘(2/2)](https://mangkyu.tistory.com/119)
[Naver D2 - Java Reference 와 GC](https://d2.naver.com/helloworld/329631)
[Baeldung - JVM Garbage Collectors(영어 블로그)](https://www.baeldung.com/jvm-garbage-collectors)