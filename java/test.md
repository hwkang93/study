> 테스트 클래스 작성 시 사용했던 어노테이션, 메소드 등을 정리
>
> 새로 알게 된 내용은 틈틈이 추가할 예정

### @SpringBootTest

- SpringBoot 어플리케이션의 테스트를 진행하기 위해 사용하는 어노테이션
- ```@SpringBootTest``` 에 사용 가능한 옵션
  - webEnvironment
    - RANDOM_PORT : WebApplicationContext 를 생성하고, port 는 0으로 설정함
    - DEFINED_PORT : WebApplicationContext 를 생성하고 어플리케이션에서 정의한 포트로 설정함
    - NONE : WebApplicationContext 를 생성하고 어플리케이션 타입을 NONE 으로 설정함
    - MOCK : Mock Servlet 환경으로 WebApplicationContext 생성
  
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
