# JWT

Json Web Token 의 약자로, 클라이언트-서버 간의 정보를 암호화된 JSON 객체로 주고받을 수 있게 정의된 개방형 표준(RFC7519)이다.
클라이언트-서버 간의 인가(Authorization)를 위해 주로 사용되며, 필요에 따라 데이터를 주고받기도 한다.

## JWT 의 구조

JWT 는 . 으로 구분된 헤더 + 내용 + 서명으로 구성되어 있으며 Base64로 인코딩되어 있다.

```
xxxxxxx.yyyyyyy.zzzzzzz
```

- 헤더 (Header)

- 내용 (Payload)

- 서명 (Signature)

## JWT 의 특징

- Stateless 하기 때문에, 서버가 확장되더라도 문제없이 사용이 가능하다.
- HttpSession 과 달리 서버에서는 토큰의 유효성만 검증한다. 따라서 서버 자체의 자원을 거의 소모하지 않는다. 
- 다른 토큰(SWT, SAML)에 비해 크기가 작다.
- JSON 객체이기 때문에 브라우저의 영향을 적게 받는다.
- 모바일에 특화되어 있다.

## 주의사항

- JWT 객체는 암호화되어있지만, 보안에 완벽하지 않기 때문에 민감한 데이터는 담겨있지 않아야 한다.
- 위와 같은 이유로 JWT 의 유효 기간이 너무 길면 안된다.
- 너무 많은 데이터를 페이로드에 담아놓으면 '경량' 이라는 JWT 만의 이점이 사라질 수 있다. 꼭 필요한 데이터만 담자.

## 예제

## Access Token 과 Refresh Token 에 대하여



~~JWT 는 Stateless 하다는 특징을 가지고 있다. 그렇기 때문에 토큰 그 자체에 어플리케이션에 대한 인가 등 모든 정보를 담고 있어야 한다.~~
토큰의 탈취 위험을 최소화하기 위해 JWT 는 Access-Token 과 Refresh-Token 으로 구분하여 관리할 수도 있다.




- Access-Token 은 클라이언트에 제공하는 토큰으로 Refresh-Token 에 비해 유효기간이 매우 짧다.
- Refresh-Token 은 서버(DB 등과 같은 저장소)에 저장되어 있는 토큰이며, Access-Token 에 비해 유효기간이 길다. Access-Token 을 재발급하는 용도로만 사용한다.

이러한 특성을 가진 두가지 토큰을 이용하면, 다음과 같은 매커니즘으로 권한의 인가를 판단할 수 있다.

1. 클라이언트는 서버에 Access-Token 값으로 정보를 요청한다.
2. 서버는 Access-Token 값이 유효한지 검증한다. 만약 유효하다면 Access-Token 과 함께 정보를 반환한다.
3. Access-Token 값이 유효하지 않다면 Access-Token 값을 바탕으로 Refresh-Token 값을 조회한다.
4. Refresh-Token 값이 유효하다면, Access-Token 을 새로 발급하고 요청한 정보와 함께 리턴한다. 이 때, Refresh-Token 에 매핑된 Access-Token 값도 최신화한다.
5. Refresh-Token 값이 유효하지 않다면 서버에 저장된 Refresh-Token - Access-Token 을 폐기하고 로그인 화면으로 리턴한다.

그럼 다음과 같은 보안상의 이점을 얻을 수 있다.

- 비교적 유효기간이 긴 Refresh-Token 은 서버에서 관리하기 때문에 공격자가 탈취하기 어렵다.
- 클라이언트에 제공하는 Access-Token 은 유효기간이 매우 짧기 때문에 공격자가 토큰을 탈취하더라도 토큰의 사용 시간을 줄일 수 있다.



## Reference

http://www.opennaru.com/opennaru-blog/jwt-json-web-token-with-microservice/

http://www.opennaru.com/opennaru-blog/jwt-json-web-token/

https://jwt.io/introduction

https://techdocs.broadcom.com/kr/ko/symantec-security-software/identity-security/siteminder/12-8/464989729/464989730/464989742/464989775.html

https://velog.io/@park2348190/JWT%EC%97%90%EC%84%9C-Refresh-Token%EC%9D%80-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%9C%EA%B0%80