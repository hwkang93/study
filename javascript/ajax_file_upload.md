# AJAX 파일 업로드

다음은 AJAX 로 파일 업로드하는 소스코드 예제이다.

```html
<input type="file" id="file">
```

```javascript
const file = document.querySelector('#file')[0].files;
const form = new FormData();
form.append('file', file);

$.ajax({
    'type' : 'post',
    'url' : url + queryParams,
    'contentType' :false,
    'processData' : false,
    'data' : form,
    'success' : function(response) {
        console.log(response)
    },
    'error' : function (xhr, status, err) {
        console.error(xhr);
        console.error(status);
        console.error(err);
    }
})
```

여기서 아래 부분에 대한 설명을 기록할 필요가 있어 보인다.

```
'contentType' :false,
'processData' : false,
```

### ContentType

서버로 AJAX 요청 시 Request Header 에는 ```Content-Type``` 이 파라미터로 전송된다. 디폴트 값은 text 이며 파일을 업로드 할 때에는 ```multipart/form-data``` 여야 한다.
multipart/form-data 는 웹 폼 데이터를 서버로 전송하기 위한 Content-Type 중 하나로, 파일 업로드와 같이 바이너리 데이터를 포함하는 경우에 사용된다.

하지만 서버로 요청할 때 ```'Content-Type':'multipart/form-data'``` 이 아닌 ```Content-Type: false``` 여야 정상적인 파일 전송이 가능하다.

실제 서버에 요청하는 Request Header 를 확인해보면 이유를 짐작할 수 있다.

```
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryDiK1i1O1x8IBp5aI
```

서버로 파일을 전송하게 되면 Content-Type 에 boundary 가 추가되어 있음을 알 수 있다. 

이 경우, 데이터를 전송하는 방식이 일반적인 텍스트 데이터와는 다르기 때문에, Ajax 요청을 설정할 때 content-type을 multipart/form-data로 지정해주어야 한다.

그러나 Ajax는 기본적으로 텍스트 데이터를 전송하기 위해 설계된 기술이기 때문에, multipart/form-data와 같은 바이너리 데이터를 처리하는 능력이 제한적이다. 
따라서 multipart/form-data를 사용하는 경우, Ajax가 자동으로 데이터를 처리하지 않고, 브라우저가 데이터를 직접 처리하도록 content-type을 false로 설정해야 한다. 

content-type을 false로 설정하는 것은, Ajax가 요청 헤더에 Content-Type 헤더를 포함시키지 않도록 하여, 
브라우저가 자동으로 multipart/form-data 형식으로 데이터를 처리하도록 하는 것이다. 이렇게 함으로써 브라우저는 요청을 multipart/form-data로 처리하고, 
서버에서는 정상적으로 multipart/form-data로 전송된 데이터를 처리할 수 있게 된다.

### ProcessData

AJAX 의 processData 의 디폴트 값은 true 이다. 그러면 AJAX 는 데이터를 서버로 보낼 때 스트링 문자열 형태로 인코딩하여 전송하게 된다. 
그러나 파일의 경우 쿼리 문자열로 인코딩될 수 없기 때문에 정상적으로 파일이 서버로 전송되지 않는다. 그렇기 때문에 processData 를 false 로 지정하고
Form 데이터를 통해 서버로 파일을 전송할 수 있다. 