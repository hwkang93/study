# Swagger

문서를 따로 관리하지 않아도 됨

UI 를 제공해줘서 편함

테스트를 해볼 수 있음

컨트롤러에서는 주석을 따로 작성하지 않아도 된다는 깨알같은 장점이 있음

정해진 양식이 있어 표준화하는 데에도 도움이 됨

## 주요 어노테이션

Swagger API 는 어노테이션을 기반으로 기능을 구현한다.



## 주요 어노테이션



## 실습

### 1. 프로젝트 만들기

[Spring Initializr](https://start.spring.io) 페이지에 접속하여 스프링부트 프로젝트를 하나 만들어준다.
최초 프로젝트 생성 스펙은 다음과 같다.

- Gradle Project
- Java 11
- Spring Boot 2.6.3
- jar
- ( + Lombok Library)

> 버전에 따른 에러 발생으로 인해 다운그레이드한 스펙들이 있다. 자세한 사항은 아래에서 설명하겠다.

### 2. swagger 의존성 추가

다음은 ```build.gradle``` 의 ```plugins```, ```dependencies``` 목록이다. 


```build.gradle```

```
plugins {
	/*id 'org.springframework.boot' version '2.6.3'*/
	id 'org.springframework.boot' version '2.4.0'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

...

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'

	compileOnly 'org.projectlombok:lombok:1.18.22'
	annotationProcessor 'org.projectlombok:lombok:1.18.22'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'

	//https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui
	implementation group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.9.2'
	implementation (group: 'io.springfox', name: 'springfox-swagger2', version: '2.9.2') {
		exclude module: 'swagger-annotations'
		exclude module: 'swagger-models'
	}

	implementation 'io.swagger:swagger-annotations:1.5.21'
	implementation 'io.swagger:swagger-models:1.5.21'

}
```

잠깐 설명하고 넘어가자면,
- SpringBoot 와 Swagger 간의 버전 차이로 인해 SpringBoot 의 버전을 2.6.3 -> 2.4.0 으로 다운그레이드
- lombok 라이브러리에서 오류가 발생하여 1.18.12 로 버전을 명시해줬다. (start.spring.io 에서 롬복 의존성을 추가하면 버전 명시가 따로 돼있지 않음)
- 콜솔창에 오류가 출력되어(기능상의 문제는 없지만) Swagger API 의 기본 버전은 2.9.2 로 하되 annotations, models 는 1.5.21 로 명시하였다.

> 오류 발생 상황은 아래에서 따로 정리했다.

### 3. 기능 구현

기능 구현은 간단하게 사용자 목록 조회, 사용자 검색, 사용자 추가, 사용자 삭제 기능만 구현했다.
데이터 관리를 위해 RDBMS 는 사용하지 않았고, 메모리에서 작업하는 방식을 선택했다.

기능 구현 전 먼저 테스트케이스를 작성한다.

```userTest.java```

```java
@SpringBootTest
public class UserTest {

    @Autowired
    UserService userService;

    @Test
    void 사용자_목록_조회_성공() {
        List<UserDto> userList = userService.findAll();

        assertThat(userList.size()).isEqualTo(1);
    }
    @Test
    void 사용자_검색_성공() {
        UserDto userDto = userService.findByUserId(1);

        assertThat(userDto.getUserName()).isEqualTo("khw");
    }

    @Test
    void 사용자_검색_실패_존재하지않는사용자() {

        assertThrows(NoSuchElementException.class, () -> userService.findByUserId(10));
    }

    @Test
    void 사용자_추가_성공() {
        UserDto paramUser = UserDto.builder()
                .userName("chr")
                .age(30)
                .build();

        userService.insertUser(paramUser);

        List<UserDto> userDtoList = userService.findAll();

        assertThat(userDtoList.size()).isEqualTo(2);
    }

    @Test
    void 사용자_삭제_성공() {
        long id = 1;
        UserDto userDto = userService.findByUserId(id);

        System.out.println(userDto);
        assertThat(userDto.getUserName()).isEqualTo("khw");

        userService.deleteUser(id);

        assertThrows(NoSuchElementException.class, () -> userService.findByUserId(id));
    }
}
```

테스트 케이스에 맞는 서비스를 인터페이스를 생성한다.

```UserService.java```

```java
public interface UserService {

    List<UserDto> findAll();

    UserDto findByUserId(long userId);

    UserDto insertUser(UserDto userDto);

    void deleteUser(long userId);
}
```

주제와 벗어나는 내용을 최소화하기 위해 구현체는 
[GitHub](https://github.com/hwkang93/springboot-swagger/blob/master/src/main/java/hwkang/springbootswagger/user/service/impl/UserServiceImpl.java) 
에 올려놓을 테니 참고하실 분들은 참고해주시면 되겠다.

테스트가 정상적으로 이루어지는지 확인한다.

(Image)

이제 Swagger 를 구현할 차례이다.
어노테이션 기반으로 UI 가 생성되는데, 대부분의 어노테이션은 컨트롤러에 작성한다.

컨트롤러를 구현하기 전에, 먼저 설정 Java 파일을 하나 만들어준다.

```SwaggerConfig.java```

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    private final String TITLE = "SpringBoot-Swagger Demo";
    private final String VERSION = "1.0.0";
    private final String DESCRIPTION = "SpringBoot-Swagger Demo Project";

    @Bean
    Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .useDefaultResponseMessages(false)  //UI 상에서 디폴트 HttpStatus 메시지 안보이게 하기
                .select()
                .apis(RequestHandlerSelectors.basePackage("hwkang.springbootswagger"))
                .paths(PathSelectors.any())
                .build();
    }

    @Bean
    public UiConfiguration ui() {

        return UiConfigurationBuilder.builder()
                .defaultModelsExpandDepth(-1)   //화면 하단에 Models Section 숨기기
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title(TITLE)
                .version(VERSION)
                .description(DESCRIPTION)
                .build();
    }
}
```

위의 소스코드는 Swagger 메인 화면에 표출될 정보 및 Swagger API 의 적용 범위, 기타 기본적인 설정 정보를 결정하고,
Swagger API 를 활성화시키는 내용의 소스코드이다.

다음으로 컨트롤러를 작성한다. 컨트롤러에 작성된 어노테이션을 기반으로 Swagger API UI 가 만들어진다.

```UserController.java```

```java
@RestController
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @ApiOperation(
            value = "사용자 목록 조회",
            notes = "사용자 전체 목록을 조회하는 기능입니다.",
            response = UserDto.class,
            responseContainer = "List"
    )
    @GetMapping("/users")
    public ResponseEntity<BasicResponse> findALl() {
        List<UserDto> userList = userService.findALl();

        return ResponseEntity.ok().body(new SuccessResponse<>(userList));
    }

    @ApiOperation(
            value = "사용자 검색",
            notes = "사용자 아이디로 사용자를 조회하는 기능입니다. <br> 사용자가 없을 시 NoSuchElementException 을 리턴합니다.",
            response = UserDto.class
    )
    @ApiImplicitParam(name = "userId", value = "사용자 아이디", defaultValue = "1", dataType = "string", required = true)
    @GetMapping("/users/{userId}")
    public ResponseEntity<BasicResponse> findByUserId(@PathVariable String userId) {
        long paramUserId = Long.parseLong(userId);
        UserDto response = userService.findByUserId(paramUserId);

        return ResponseEntity.ok().body(new SuccessResponse<>(response));
    }

    @ApiOperation(
            value = "사용자 추가",
            notes = "사용자를 추가하는 기능입니다."
    )
    @ApiImplicitParams({
            @ApiImplicitParam(name = "userName", value = "사용자 명", dataType = "string", paramType = "query", required = true),
            @ApiImplicitParam(name = "age", value = "나이", dataType = "int", paramType = "query", required = true)
    })
    @PostMapping(value = "/users")
    public ResponseEntity<Void> insertUser(@ApiIgnore UserDto userDto) {
        UserDto insertedUser = userService.insertUser(userDto);
        String location = "users/" + insertedUser.getUserId();

        return ResponseEntity.created(URI.create(location)).build();
    }

    @ApiOperation(
            value = "사용자 삭제",
            notes = "사용자를 삭제하는 기능입니다."
    )
    @ApiImplicitParam(name = "userId", value = "사용자 아이디", defaultValue = "1", dataType = "string", required = true)
    @DeleteMapping("/users/{userId}")
    public ResponseEntity<Void> deleteUser(@PathVariable String userId) {
        long paramUserId = Long.parseLong(userId);

        userService.deleteUser(paramUserId);

        return ResponseEntity.ok().build();
    }
    
    @ExceptionHandler(NoSuchElementException.class)
    public ResponseEntity<BasicResponse> noSuchElementExceptionHandler(NoSuchElementException exception) {
        final String message = "존재하지 않는 사용자입니다.";

        return ResponseEntity.badRequest().body(new ErrorResponse(message));
    }
}
```

마지막으로 서버를 기동한 후, localhost:8080/swagger-api.html 에 접속하여 정상적으로 Swagger API UI 가 생성됐는지 확인한다.


## 예제 만들면서 발생한 오류 정리

다음은 예제 프로젝트 구현 중 발생한 오류들을 정리한 것이다.

### org.springframework.context.ApplicationContextException: Failed to start bean 'documentationPluginsBootstrapper

- SpringBoot 와 Swagger 간의 버전 충돌이 원인
- **SpringBoot 의 버전을 2.6.3 -> 2.4.0 으로 다운그레이드 후 해결**

```build.gradle```

```
plugins {
	/*id 'org.springframework.boot' version '2.6.3'*/
	id 'org.springframework.boot' version '2.4.0'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}
```

### java: java.lang.IllegalAccessError: class lombok.javac.apt.LombokProcessor

- jdk 컴파일러가 롬복 모듈에 접근할 수 없다는 오류
- **롬복 라이브러리 버전을 명시하는 방법으로 해결 (롬복 버전을 1.18.22 로 명시)**

```build.gradle``` 

```
compileOnly 'org.projectlombok:lombok:1.18.22'
annotationProcessor 'org.projectlombok:lombok:1.18.22'
```

### java.lang.NumberFormatException: For input string: ""

- Swagger API 를 사용하는 데 지장은 없지만 로그에 자꾸 에러가 찍혀서 찾아봄
- swagger2 에서 던지는 에러에 대한 핸들링이 제대로 이루어지지 않는 것이 원인이라고 함
- **swagger 버전은 2.9.2 를 쓰되 swagger-models, swagger-annotation 버전은 1.5.21 로 다운그레이드 후 해결**

```build.gradle```

```
implementation group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.9.2'
implementation (group: 'io.springfox', name: 'springfox-swagger2', version: '2.9.2') {
    exclude module: 'swagger-annotations'
    exclude module: 'swagger-models'
}

implementation 'io.swagger:swagger-annotations:1.5.21'
implementation 'io.swagger:swagger-models:1.5.21'
```
 

## Reference

[스프링부트 Swagger UI 적용 방법 - 스프링부트 2.2 미만 (Spring Boot Swagger UI)](https://nahwasa.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-Swagger-UI-%EC%A0%81%EC%9A%A9-%EB%B0%A9%EB%B2%95-Spring-Boot-Swagger-UI)

[[Swagger 2] java.lang.NumberFormatException:For input string: "" exception 해결법](https://earth-95.tistory.com/m/114)