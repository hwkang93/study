# Fetch API

HTTP Request, Response 를 간단한 파이프라인 코드를 통해 구현하는 API 로```XMLHttpRequest``` 의 대체제로 사용된다.

Fetch API 의 기본 스펙은 JQuery 에서 제공하는 ajax 와 두 가지 차이점이 있다.

- **HTTP Error 상태를 reject 하지 않는다.**

```fetch()``` 메소드를 호출하면 ```promise()``` 객체가 리턴되는데
여기서 Http 상태 코드가 400번대 혹은 500번대가 리턴되더라도 ```reject()``` 메소드가 호출되는 게 아니고 결과 상태가 ok 가 아닌 ```resolve()``` 메소드가 호출된다.
```reject()``` 메소드는 네트워크 장애나 요청이 완료되지 못한 상태에 호출된다.

- **쿠키를 보내거나 받지 않는다.**

쿠키를 전송하기 위해서는 credentials 옵션을 반드시 설정해줘야 한다.

## Fetch API 예제

다음은 기본적은 ```GET``` 요청 방식이다.

```javascript
    const userId = 'hwkang';
    const url = `/users/${userId}`

    fetch(url)
        .then(response => response.json())
        .then(response => console.log(response))
        .catch(error => console.error(error));
```

최초 결과는 ```json``` 이 아닌 HTTP Response 객체이다. 위의 예제처럼 HTTP Response 객체를 json 객체로 파싱하여 사용할 수 있다.

```fetch()``` 메소드는 URL, RequestOptions 두 개의 파라미터를 받는다.




## Reference

[Mozilla - Using Fetch](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Using_Fetch)