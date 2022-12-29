# AJAX 로 파일 업로드 하는 예제 소스코드

- js
  - 파일을 서버에 업로드하기 위해서는 ```FormData``` 을 이용한다.
  - 브라우저가 Content-Type 을 자동으로 설정하도록 해야 하기 때문에 false 를 지정한다.
  - ```processData : false``` 로 지정한다.

```javascript
function insert(fnCallback) {
	const url = '/users';
	const username = 'khw';
	var file = document.querySelector('#file').files[0];
    
	var formData = new FormData();
	formData.append('username', username);
	formData.append('file', file);

	$.ajax({
		  data 	        : formData
		, url 	        : url
		, contentType	: false
		, processData 	: false
		, type 	        : 'post'
		, success       : function(response){
			fnCallback('success', '사용자 등록에 성공하였습니다.')
		}
		, error 	: function(request, status, error) {
			fnCallback('error', request.responseText);
		}
	})

}
```

- java
  - @ResponseBody 어노테이션은 json 객체를 파싱하기 위한 어노테이션으로 Form 객체를 받기 위해서는 사용하지 않아야 한다.
  - 파일 이외의 데이터를 받기 위해 @ModelAttribute 어노테이션을 사용한다.

```java
@Controller
class UserController {
    //...
    
    @PostMapping("/users")
    public ResponseEntity<String> insert(HttpServletRequest request, HttpServletResponse response, 
                                         @ModelAttribute UserDto userDto, @RequestParam("file") MultipartFile file) {

        //...
        return ResponseEntity.ok().build();
    }
    
    //...
}
```