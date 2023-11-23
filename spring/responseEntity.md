# ResponseEntity





### ResponseType 이 String 인 경우 인코딩 설정 예시

응답 객체에 ```MediaType.APPLICATION_JSON_UTF8``` 을 추가해주는 예제 소스코드이다.

```java
@PostMapping("/login")
public ResponseEntity<String> login(HttpServletRequest request, HttpServletResponse response, LoginRequestDTO requestDTO) {
    try {
        authService.login(requestVO);
    } catch (RuntimeException e) {
        String message = e.getMessage();

        return ResponseEntity
                .badRequest()
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .body(message);
    }

    return ResponseEntity.ok("success");
}
```