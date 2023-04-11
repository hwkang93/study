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

서버로 AJAX 요청 시 Request Header 에는 ```Content-Type``` 이 파라미터로 전송된다.
디폴트 값은 text 이며 파일을 업로드 할 때에는 ```multipart/form-data``` 여야 한다.


### ProcessData

AJAX 는 