# Strategy Pattern

> 이번에 개인 프로젝트 진행 중에 발생한 이슈를 해결하기 위한 방법으로 전략 패턴(Strategy Pattern)을 사용했다.
> 

전략 패턴(Strategy Pattern) 이란 실행 중에 알고리즘을 선택할 수 있게 하는 행위 소프트웨어 디자인 패턴이다.

전략 패턴은
- 동일한 기능을 하는 알고리즘들을 정의하고
- 각 알고리즘을 캡슐화하며
- 이 알고리즘들을 해당 계열 안에서 상호 교체가 가능하게 만든다.



## 전략 패턴을 사용해야 하는 이유


## 예제

> 개인 프로젝트를 진행하면서 실제로 구상했던 상황을 재구성하여 예제로 사용하려 한다. 
> 물론 설계 후 실제로 구현은 불가능했지만 확장성있는 설계를 시도했다는 것에 의의를 둔다.
> 
> 처음 계획했던 건, 사용자는 구글, 네이버, 카카오로 로그인을 할 수 있고 로그인을 하면 해당 사용자의 드라이브에 접근하여 그 드라이브의 사진 목록을 조회하는 것이었다. 
> 
> 하지만 네이버의 경우 NDrive API 를 제공하지 않았고, 카카오는 드라이브 자체가 유료였으며, 구글의 경우 서비스별 클라이언트 이메일이 별도로 존재하고,
> 그 이메일에 공유된 폴더만 접근이 가능했고 사용자별 드라이브 접근은 불가능했다.
> 
> 그래서 처음 설계했던 대로 진행하지는 못했지만, 전략 패턴을 실제 코드로 처음 접했고, 이 글을 쓰는 시점이 코드로 접한 전략 패턴을 통해
> 따로 전략 패턴에 대해 정리할 필요가 있었음을 느낀 때이기 때문에 그 때의 코드로 예제를 들려 한다.
> 
> 모든 코드가 그렇듯 이 코드가 정답은 아니며, 더 나은 코드도 있을 수 있고 더 나은 방법이 있을 수 있다.
> 그냥 참고만 해주셨으면 한다.

사용자가 서비스를 이용하기 위해서는 로그인을 해야 한다. 로그인을 하는 방법은 구글, 네이버, 카카오가 있으며 해당 플랫폼으로 로그인을 하면,
로그인한 플랫폼의 드라이브로 접근을 하여 드라이브에 저장된 사진 목록을 조회하는 기능이다. 

**여기서, 사용자가 로그인에 성공하는 시점 (런타임 시점) 에 사용자의 플랫폼 서비스 객체를 결정해야 한다.**

우선, ```PictureApiService``` 라는 사진 조회 인터페이스를 생성하고, 
```GooglePictureApiService``` , ```NaverPictureApiService``` , ```KakaoPictureApiService``` 라는 플랫폼 각각의 구현체를 생성하고 스프링 빈으로 등록한다. 
또한 공통 DTO 클래스가 필요하기 때문에 ```Picture``` 클래스도 생성한다. 

```java
public interface PictureApiService {
    
    public List<Picture> pictureList() throws IOException;
    
    public Picture findById(String id) throws IOException;
}
```

```java
@Service
public class GooglePictureApiService implements PictureApiService {
    
    @Override
    public List<Picture> pictureList() throws IOException {
        //...
    }
    
    @Override
    public Picture findById(String id) throws IOException {
        //...
    }
}
```

```java
@Service
public class NaverPictureApiService implements PictureApiService {
    
    @Override
    public List<Picture> pictureList() throws IOException {
        //...
    }
    
    @Override
    public Picture findById(String id) throws IOException {
        //...
    }
}
```

```java
@Service
public class KakaoPictureApiService implements PictureApiService {
    
    @Override
    public List<Picture> pictureList() throws IOException {
        //...
    }
    
    @Override
    public Picture findById(String id) throws IOException {
        //...
    }
}
```

```java
@Getter
public class Picture {
    //...
}
```
<br/>

그리고 사용자가 로그인을 했을 때 해당 플랫폼에 맞는 서비스 빈 객체를 조회해 사용자 세션 객체에 추가해주면 된다. 
아래 예제 소스의 경우, 로그인한 사용자의 플랫폼 종류 ```registrationId``` 를 조회하고, 그 ```registrationId``` 에 맞는 빈 객체를 찾는다.
그리고 그 빈을 세션 객체에 추가한다.

먼저 플랫폼 종류에 맞는 빈 객체를 조회하기 위해 ```OAuthType``` 열거형을 생성한다.

```java
@RequiredArgsConstructor
@Getter
public enum OAuthType {
    GOOGLE("google", "googlePictureService"),
    NAVER("naver", "naverPictureService"),
    KAKAO("kakao", "kakaoPictureService");

    private final String registrationId;
    private final String pictureServiceBeanName;

    public static OAuthType findBy(String registrationId) {

        return Arrays.asList(OAuthType.values()).stream()
                .findFirst()
                .orElseThrow(() -> new IllegalArgumentException("Not found OAuthType. Request registrationId : " + registrationId));
    }
}
```

<br/>

그리고 ```OAuth2UserService``` 인터페이스를 구현한다.

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class CustomOAuth2UserService implements OAuth2UserService<OAuth2UserRequest, OAuth2User> {

    private final HttpSession httpSession;
    //1. pictureService 구현체 Bean 객체를 Map 에 할당
    private final Map<String, PictureService> pictureServiceMap;

    @Override
    public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
        OAuth2UserService delegate = new DefaultOAuth2UserService();
        OAuth2User oAuth2User = delegate.loadUser(userRequest);
        
        //2. 플랫폼 ID 조회 (google, naver, kakao...)
        String registrationId = userRequest.getClientRegistration().getRegistrationId();
        String userNameAttributeName = userRequest.getClientRegistration()
                .getProviderDetails()
                .getUserInfoEndpoint()
                .getUserNameAttributeName();
        
        //3. 플랫폼에 맞는 OAuthType 조회 
        OAuthType oAuthType = OAuthType.findBy(registrationId);
        
        //4. 해당 타입의 beanName 조회
        String beanName = oAuthType.getPictureServiceBeanName();
        
        //5. beanName 으로 스프링 bean 으로 등록된 PictureService 구현체 조회 
        PictureService pictureService = pictureServiceMap.get(beanName);
        
        OAuthAttributes attributes = OAuthAttributes.of(oAuthType, userNameAttributeName,
                oAuth2User.getAttributes(), pictureService);
        
        //6. Session 객체에 추가
        httpSession.setAttribute("user", new SessionUser(attributes));

        return new DefaultOAuth2User(
                Collections.singleton(new SimpleGrantedAuthority(Role.LOGIN_USER.getKey())),
                attributes.getAttributes(),
                attributes.getNameAttributeKey()
        );
    }
}
```

이런 방법으로 런타임 시점에 동적으로 객체를 할당할 수 있었다.

## 마치며

이전에 작성했던 다른 내용들과는 반대로 이번 주제는 소스코드를 작성하는 중에 문제가 발생했고 문제를 해결하기 위해 찾은 해결책이었다.
그래서 경험했던 내용을 예제로 꼭 담고 싶었는데, 담다보니 글이 길어지고 주제에 벗어나는 내용들이 많아진 것 같다.
시간이 조금 지나고 이 글을 다시 볼 때 '너무 갔다' 싶으면 예제를 바꾸려고 한다.


## Reference

[위키백과 - 전략 패턴](https://ko.wikipedia.org/wiki/%EC%A0%84%EB%9E%B5_%ED%8C%A8%ED%84%B4)

[꿩이야기 - Strategy Pattern](https://scorpio-mercury.tistory.com/21)