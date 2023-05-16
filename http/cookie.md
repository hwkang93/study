# Cookie

쿠키는 서버가 클라이언트의 브라우저에게 보내는 데이터를 말한다. 쿠키는 브라우저가 HTTP 요청이 있을 때마다 서버로 전송되며, 이전 요청과 관련된 정보를 기억한다.

서버에 데이터가 저장되는 세션과 반대로 클라이언트(브라우저)에 데이터가 저장되기 때문에, 어플리케이션에서 사용하는 자원에 대한 이점이 있고, 
서버의 무상태성을 보장할 수 있다. 하지만 클라이언트(브라우저)에 데이터가 저장되기 때문에 보안상의 이슈 가능성이 존재한다.

쿠키는 Key-Value 형태로 관리하며, HTTP 요청이 있을 경우 헤더에 담겨 서버로 전송된다.

## 속성(Attributes)

쿠키의 속성에 대해 정리할 필요가 있다고 판단해 정리해본다.

- Name: 쿠키의 이름
- Value: 쿠키에 저장되는 값
- Expires: 쿠키의 만료 날짜와 시간
- Max-Age: 쿠키의 유효 기간(초 단위)
- Domain: 쿠키를 전송할 도메인 이름
- Path: 쿠키를 전송할 URL 경로
- Secure: HTTPS 프로토콜만 사용할 경우 true, 아니면 false
- HttpOnly: JavaScript 를 통해 쿠키에 접근할 수 없도록 설정할 경우 true, 아니면 false

쿠키의 속성에 대해 알고 어느정도 인지하고 있는다면, 아래의 문제를 맞닥트렸을 때 같은 고생을 하지 않길 바란다.  

### 만약 쿠키가 HTTP 요청 객체에 포함되지 않는다면? 

올바른 Path 인지 확인할 필요가 있다. 쿠키를 생성할 때 Path 를 설정하지 않는다면, 디폴트 값으로 요청 URL 이 Path 로 지정된다.
그럼 쿠키는 같은 URL 에 대해서만 유효하다고 판단하고, 다른 경로의 요청에 대해서는 쿠키를 전송하지 않는다.

모든 요청에 대해 쿠키를 적용하려면 Path 로 ```/``` 를 적용하면 된다. 하지만 **전역** 이라는 것은 보안에 문제가 될 수 있으므로,
신중히 고민하고 설정하는 것이 좋겠다.

다음은 Spring Framework 에서 Path 를 전역으로 설정하는 소스코드이다.

```java
private Cookie createGuestCookie(String guestID) {
    Cookie cookie = new Cookie("id", guestID);
    cookie.setPath("/");
    cookie.setSecure(true);
    cookie.setHttpOnly(true);
    
    return cookie;
}
```

### 쿠키는 브라우저에 저장되잖아. 그런데 왜 document.cookie 에서 몇 개만 보이는 거야?

쿠키를 생성할 때 HttpOnly 를 true 로 설정하면 자바스크립트의 document.cookie 명령어로 쿠키를 조회하더라도, 쿠키가 정상적으로 조회되지 않는다.