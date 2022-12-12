# AJAX 로 파일 업로드 시 발생하는 에러

AJAX 로 파일을 업로드할 때 자주 마주치게 되는 에러 두 가지가 있다.

## 1
> **org.apache.tomcat.util.http.fileupload.FileUploadException: the request was rejected because no multipart boundary was found**
> 
> (Request 에서 Multipart boundary 를 찾을 수 없습니다.)

## 2
> **org.springframework.web.multipart.MultipartException: Current request is not a multipart request**
> 
> (Multipart Request 가 아닙니다.)


## 원인

원인은 Request Header 의 Content-Type 때문이다.
AJAX 에서 Form 데이터를 서버로 전송할 때, AJAX Header 에 **Content-Type 을 multipart/form-data 로 설정하면 1 번의 예외가 발생**하고,
**application/x-www-form-urlencoded 로 설정하면 2번의 예외가 발생**한다.

## 해결 방안

**Content-Type 을 명시하지 않으면 된다.** Form 내에 파일이 있을 경우 브라우저에서 boundary 를 자동으로 붙여주게 되는데,
사용자가 직접 명시하면 오버라이드되어 브라우저에서 만들어진 boundary 가 사라지게 된다.

## Reference

https://aljjabaegi.tistory.com/641