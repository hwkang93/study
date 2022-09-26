> 테스트 클래스 작성 시 사용했던 어노테이션, 메소드 등을 정리
>
> 새로 알게 된 내용은 틈틈이 추가할 예정

### @SpringBootTest

- SpringBoot 어플리케이션의 테스트를 진행하기 위해 사용하는 어노테이션
- ```@SpringBootTest``` 에 사용 가능한 옵션
  - webEnvironment
    - RANDOM_PORT : WebApplicationContext 를 생성하고, port 는 0으로 설정함
    - DEFINED_PORT : WebApplicationContext 를 생성하고 어플리케이션에서 정의한 포트로 설정함
    - NONE : WebApplicationContext 를 생성하고 어플리케이션 타입을 NONE 으로 설정함.
    - MOCK : Mock Servlet 환경으로 WebApplicationContext 생성.
  > RANDOM_PORT 와 DEFINED_PORT 는 WebApplicationContext 를 생성하기 때문에 다른 서버와 HTTP 통신이 가능하지만,
  > NONE, MOCK 의 경우 WebApplicationContext 가 실재하지 않기 때문에 다른 서버와 HTTP 통신이 불가능하다.   
  > 또한 RANDOM_PORT 와 DEFINED_PORT 는 테스트 스레드와, 커넥션 스레드가 별도로 존재하기 때문에 @Transactional 어노테이션을 사용하더라도,
  > 롤백이 되지 않는다. (롤백이 아예 불가능한 건 아니다.)
  

### @TestMethodOrder

- 테스트 메소드들의 순서를 정의할 수 있는 기능을 제공하는 어노테이션
- 클래스에 붙임
- ```@TestMethodOrder``` 에 사용 가능한 옵션
    - MethodName : 메소드 명칭 순서대로 진행
    - ~~DisplayName~~ : 테스트 메소드에 붙은```@DisplayName``` 어노테이션 순서대로 정렬
  > Junit 버전이 올라가면서 사라진 듯
    - OrderAnnotation : 테스트 메소드에 붙은 ```@Order``` 어노테이션 순서대로 정렬
    - Random : 랜덤으로 결정


### @DisplayName

- 테스트메소드의 명칭을 정의하는 어노테이션
- 위의 ```@TestMethodOrder``` 을 이용하면 ```@DisplayName``` 순서대로 테스트 메소드 실행 순서를 결정할 수 있다.

```java
class PostTest {
    
    @Autowired
    PostService postService;
    
    @Test
    @DisplayName("이미 삭제된 게시글을 삭제하려고 시도하면 NotFoundException 이 발생한다.")
    void tryDeleteThatNotExistPost() {
        long postId = 1;
        Post post = Post.builder()
            .postId(postId)
            .build();
        
        postService.save(post);
        Assertions.assertThat(postService.findById(postId)).isNotNull();
        
        Assertions.assertThrows(NotFoundException.class, () -> postService.delete(post));
    }
}
```



### @BeforeAll

- 테스트 클래스 시작 전에 ```@BeforeAll``` 어노테이션이 붙어있는 메소드가 먼저 실행된다.

- ```@BeforeAll``` 어노테이션이 붙은 메소드는 반드시 ```static``` 으로 선언되어 있어야 한다.

- 예를 들어 테스트 클래스에 ```@SpringbootTest``` 어노테이션이 붙어있는 경우

  > 1. ```@BeforeAll``` 어노테이션 붙은 메소드 실행
  > 2. 스프링 컨테이너 생성
  > 3. 테스트 메소드들 실행

의 순서로 실행된다.


### @BeforeEach

- 각각의 테스트 메소드 실행 전에 ```@BeforeEach``` 어노테이션이 붙은 메소드가 실행된다.
- ```@BeforeAll``` 어노테이션과는 다르게 ```static``` 으로 선언하지 않는다.


### @Transactional

- 클래스와, 메서드에 사용 가능하며, 테스트에서 사용하는 ```@Transactional``` 은 예외와 상관 없이 무조건 결과를 롤백한다.
- 위의 ```@SpringBootTest``` 에서 설명했듯, ```@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)```와 ```@SpringBootTest(webEnvironment = WebEnvironment.DEFINED_PORT)``` 에서는 일반적으로 롤백이 되지 않는다.

### @MockBean

- Spring Bean 객체를 Mock 객체로 생성한다.
- ```@SpringBootTest``` 어노테이션의 WebEnvironment=MOCK 이면서, 다른 API 와 커넥션을 갖는 Bean 객체가 있을 경우 사용한 적이 있다.

### @Rollback

- 위의 ```@Transactional``` 를 클래스에 명시하였을 경우 사용할 수 있는 어노테이션
- ```@Rollback(false)``` 로 사용하면, 테스트 메서드 종료 후에 롤백되지 않는다.
