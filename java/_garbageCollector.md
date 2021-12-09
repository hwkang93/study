# Garbage Collection

GC(Garbage Collection) 는 여러 구현 방식이 있지만, 공통적으로 다음 2가지의 작업을 수행한다.

1. Heap 메모리 내의 객체 중에서 Garbage 를 찾아낸다.
2. 찾아낸 Garbage 를 처리해서 Heap 메모리를 회수한다.

Heap 영역에서 




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


## Reference
[망나니개발자-[Java] 다양한 종류의 Garbage Collection 알고리즘(2/2)](https://mangkyu.tistory.com/119)
[Naver D2 - Java Reference 와 GC](https://d2.naver.com/helloworld/329631)
[Baeldung - JVM Garbage Collectors(영어 블로그)](https://www.baeldung.com/jvm-garbage-collectors)