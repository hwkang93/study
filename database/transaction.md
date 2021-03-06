# 트랜잭션(Transaction)

트랜잭션은 **실패와 성공이 분명히 구분된 하나의 작업 단위**를 말한다.

게시판에서 댓글이 있는 글을 삭제하는 기능을 예로 들어 정리해보자.

<br/>

글을 삭제하는 기능을 조금 더 살펴보면, 글을 삭제하는 기능 안에 글 삭제와 댓글 삭제 두 가지 기능이 합쳐져 있는 걸 알 수 있다.

이 기능의 흐름을 살펴보면

```
댓글 삭제 -> 글 삭제 -> 성공
```

대략 이런 흐름이다.

만약 댓글을 삭제하고 글을 삭제하는 도중 서비스에 장애가 발생해 글 삭제에 실패하면 어떻게 될까? 
사용자는 글을 삭제하려고 했지만 댓글만 삭제된 결과를 받게 된다.

이처럼 글 삭제는 글 삭제 뿐만 아니라, 댓글 삭제 + 글 삭제가 하나의 기능이고 하나의 작업 단위이다.
**그리고 이것을 트랜잭션(Transaction) 이라고 한다.**


## ACID

ACID 는 데이터베이스 트랜잭션이 안전하게 수행된다는 것을 보장하기 위한 성질을 가리키는 약어로,
**원자성(Atomicity), 일관성(Consistency), 독립성(Isolation), 지속성(Durability)의 줄임말이다.**

### 원자성(Atomicity)

원자성은 하나의 트랜잭션을 하나의 원자로 간주하여 트랜잭션과 관련된 작업들이 부분적으로 실행되다가 중단되지 않는 것을 보장하는 성질을 말한다.
댓글 삭제만 성공하고 글 삭제를 실패하는 일이 없어야 하는 것처럼 중간 단계까지 실행이 완료되고 트랜잭션이 끝나는 일이 없도록 하는 것이다.

### 일관성(Consistency)

트랜잭션이 성공으로 종료되면 언제나 일관성 있는 데이터베이스 상태로 유지하는 성질을 의미한다.
모든 글에 등록한 사람의 아이디가 있어야 한다면 등록자 아이디가 없는 트랜잭션은 실패로 종료되어야 한다.


### 독립성(Isolation)

트랜잭션 수행 시 다른 트랜잭션의 연산 작업이 끼어들지 못하는 걸 보장하는 성질을 의미한다.
글 삭제 트랜잭션이 진행되고 있는 도중에는 다른 사람이 삭제하고 있는 글에 댓글을 달 수 없어야 한다.

> 성능 관련 이유로 인해 이 특성은 가장 유연성 있는 제약조건이라고 한다.


### 지속성(Durability)

성공적으로 수행된 트랜잭션은 영원히 반영되어야 함을 의미한다.
글을 삭제하고 사이트를 껐다가 다시 접속하더라도 해당 글은 삭제되어 있어야 한다.





## ACID 구현 방법

기존에는 ACID 속성을 구현하기 위해 락(Lock) 방법을 주로 사용했다고 한다.

락 방법의 기본 메커니즘은

1. 데이터 처리 요청이 들어오면
2. 데이터 처리 전 데이터베이스에 락이 걸리고
3. 데이터베이스는 해당 데이터를 처리한 후
4. 데이터베이스의 락이 풀린다.

예를 들어 A 유저가 데이터를 수정하고 있다면 B 유저가 해당 데이터를 조회하기 위해서는 A 유저의 데이터 수정 기능이 끝나기를 기다려야 한다.
이러한 **락 방법은 트랜잭션을 요청하는 요청의 수가 많아지게 되면, 동시 작업 수행이 어려워지게 되고 성능 저하를 초래하게 되는 단점을 가지고 있다.**



최근에는 락 방법의 대안으로 **MVCC(다중 버전 동시성 제어) 방법**을 채택하고 있다.

MVCC 는 데이터베이스를 버전으로 관라하는 방법인데, 위와 같은 상황을 예로 들어보면

1. A 유저가 데이터를 수정하고 있으면
2. 데이터베이스는 수정된 데이터를 저장하기 위한 새로운 버전을 만든다.
3. 여기서 B 유저가 해당 데이터를 조회하게 되면,
4. A 유저의 데이터 수정은 새로운 버전에서 진행이 되고 B 유저는 기존 버전의 데이터를 조회한다.


> **다중 버전 동시성 제어 (Multiversion concurrency control, MCC, MVCC)**
>
> 데이터베이스 관리 시스템이 일반적으로 사용하는 동시성 제어 방법으로, 데이터베이스의 동시 접근을 제공하고, 프로그래밍 언어에서 트랜잭셔널 메모리를 구현한다.
>
> 데이터베이스가 데이터의 업데이트를 필요로 할 때, 기존 데이터 항목을 새로운 데이터가 덮어쓰는 대신 데이터 항목의 새로운 버전을 만들어 여러 버전으로 관리되는 방법이다.
>
> Git, SVN 등의 형상관리 툴과 비슷한 원리로 이해했다.