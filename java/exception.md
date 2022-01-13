# Exception

> 처음 겪어보는 예외에 대해 간단히 알아보고 정리하는 곳

### org.apache.catalina.connector.ClientAbortException

클라이언트의 정보가 사라져 서버가 클라이언트에게 응답을 보낼 수 없는 경우에 발생하는 예외
네트워크 끊김, 브라우저 종료, 다른 화면으로 이동 등의 원인이 있다.

### java.net.MalformedURLException

올바르지 않은 URL 을 입력해 발생하는 예외.
나의 경우에는 DB 에서 URL 을 조회하는데, DB 에 저장된 URL 값이 정상적이지 않아 발생했다. 
