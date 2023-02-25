# EntityManagerFactory

EntityManagerFactory 는 영속성 유닛(Entity Unit)에 대한 팩토리 클래스이다. 이는 Java Persistence API (JPA)에서 사용되며, 데이터베이스와의 연결 및 엔터티 관리에 사용된다.
EntityManagerFactory 는 애플리케이션에서 EntityManager 를 만들기 위한 기반이 된다. EntityManagerFactory 는 데이터베이스 연결 설정, 엔터티 클래스와 데이터베이스 테이블 간의 매핑 정보 등을 포함합니다. 
애플리케이션에서 EntityManagerFactory 를 사용하여 EntityManager 를 생성하고, 이를 통해 엔터티의 영속성 컨텍스트(Persistence Context)를 관리할 수 있다.

EntityManagerFactor y는 주로 애플리케이션의 시작 시점에 생성되며, 애플리케이션 전체의 생명주기 동안 유지되는 싱글톤 객체이다. 
애플리케이션에서 EntityManagerFactory 를 통해 EntityManager 를 생성하여 엔터티의 저장, 조회, 수정, 삭제 등의 데이터베이스 작업을 수행할 수 있다.



## Reference

ChatGPT