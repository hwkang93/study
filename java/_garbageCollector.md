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





## GC 의 동작 방식

GC 는 Heap 영역에 있는 객체를 대상으로 동작한다.
Heap 영역은 Young 영역과 Old 영역으로 나누어져 있고 각 영역은 서로 다른 메모리 구조로 되어 있기 때문에, 세부적인 동작 방식은 다르다.
하지만 기본적으로 가비지 컬렉션이 실행된다고 하면 다음 두 가지 공통적인 단계를 따르게 된다.

1. Stop The World
2. Mark and Sweep

> **Stop The World**
> 
> GC 를 하기 위해 GC 를 실행하는 쓰레드를 제외한 모든 쓰레드를 중지시키는 것을 말한다. GC 가 완료되면 작업이 재개된다.
> 일반적으로 GC 의 성능 개선을 위해 튜닝을 한다고 하면 보통 Stop The World 의 시간을 줄이는 작업을 말한다.
>
> **Mark and Sweep**
> - Mark : 사용되는 메모리와 사용되지 않는 메모리를 식별하는 작업
> - Sweep : Mark 단계에서 사용되지 않음으로 식별된 메모리를 해제하는 작업
>
> Stop The World 를 통해 모든 작업이 중지되면, GC 는 스택의 모든 변수 또는 Reachable 객체를 스캔하면서 사용되고 있는 메모리인지 식별하는 작업을 하는데,
> 이 과정을 Mark 라고 한다.
> 이후에 Mark 되지 않은 객체들을 메모리에서 제거하는데, 이러한 과정을 Sweep 라고 한다.

GC 는 크게 Young 영역에서 이루어지는 Minor GC 가 있고, Old 영역에서 이루어지는 Major GC 로 구분할 수 있다.

### Minor GC

Young 영역은 다시 1개의 Eden 영역과 2개의 Survivor 영역으로 나뉜다.
- Eden 영역 : 새로 생성된 객체가 할당되는 영역
- Survivor 영역 : 최소 한 번 이상의 Minor GC 에서 살아남은 객체가 존재하는 영역

최초의 객체는 Eden 영역에 할당된다. 그리고 Eden 영역이 꽉 차게 되면 Survivor 영역 중 하나로 이동하는데, 모든 객체가 Survivor 영역으로 이동하는 것이 아니라
Minor GC 를 진행하고 살아남은 객체만 Survivor 영역으로 이동한다. Minor GC 에서 살아남지 못한 객체의 메모리는 회수된다.

객체의 생존 횟수를 카운트하기 위해 Minor GC 에서 살아남은 횟수를 의미하는 age 를 객체의 Object Header 에 기록한다.
그리고 Minor GC 때 Object Header 에 기록된 age 를 보고 Old 영역으로의 Promotion 여부를 결정한다.

Young 영역은 Old 영역에 비해 메모리 크기가 작기 때문에 보통 0.5초 ~ 1초 사이의 GC 시간을 가지며 GC 시간이 짧기 때문에 애플리케이션에 영향을 크게 미치지 않는다.

### Major GC

Young 영역에서 살아남은 객체들은 Old 영역으로 Promotion 된다. 그리고 Old 영역의 메모리가 부족해지면 Major GC 가 발생한다.
Major GC 는 일반적으로 Minor GC 보다 10배 이상의 시간이 소요된다고 한다.




## GC 구현 방법

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

Parallel GC 는 Throughput GC 라고도 불리며, 기본적은 Serial GC 와 같지만, 멀티쓰레드 지원 유무에 차이가 있다.
Parallel GC 는 여러 개의 쓰레드를 사용해 GC 를 수행하며, GC 의 오버헤드를 줄여준다.
Parallel GC 는 Java8 까지 기본 GC 로 사용되었으나, 어플리케이션이 멈추는 부분은 지속되었고 더 나은 성능을 위해 새로운 알고리즘이
더 등장하게 되었다.

> java -XX:+UseParallelGC -jar Application.java 
> // 사용할 쓰레드의 갯수 -XX:ParallelGCThreads=<N>
> // 최대 지연 시간 -XX:MaxGCPauseMillis=<N>

### CMS Garbage Collector

CMS(Concurrent Mark Sweep) GC 는 어플리케이션의 지연 시간을 최소화하기 위해 만들어졌으며,
Parallel GC 와 마찬가지로 여러 개의 쓰레드를 이용한다.
하지만 위의 두 GC 와는 다르게 Mark Sweep 알고리즘을 Concurrent 하게 수행한다.

CMS GC 는 다른 GC 방식보다 메모리와 CPU 를 더 많이 필요로 하며, Compaction 단계를 수행하지 않는다는 단점이 있다.
그래서 시스템이 장기적으로 운영되어 가비지가 많아지면 오히려 Stop The World 시간이 길어지는 문제가 발생할 수 있다.

CMS GC 는 Java9 버전부터 deprecated 되었으며, Java14 버전에서는 사용이 중지되었다.

> java -XX:+UseConcMarkSweepGC -jar Application.java


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