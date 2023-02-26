# X-Forwarded-For(XFF)

Forwarded-For(XFF) 헤더는 HTTP 요청 헤더 중 하나로, 클라이언트의 IP 주소를 식별하는 데 사용된다.

주로 프록시 서버나 로드 밸런서를 사용할 때 클라이언트의 원래 IP 주소를 전달하기 위해 사용된다.



프록시 서버나 로드 밸런서를 거친 경우, 웹 서버는 클라이언트의 실제 IP 주소가 아닌 프록시 서버나 로드 밸런서의 IP 주소를 받게 됩니다.

X-Forwarded-For 헤더는 이러한 문제를 해결하기 위해 사용되며,

프록시 서버나 로드 밸런서가 이 헤더를 추가하고 클라이언트의 원래 IP 주소를 전달합니다.



웹 서버는 X-Forwarded-For 헤더 값을 사용하여 클라이언트의 실제 IP 주소를 얻을 수 있습니다.

예를 들어, X-Forwarded-For 헤더는 다음과 같이 표시될 수 있습니다.



X-Forwarded-For: client_ip, proxy1_ip, proxy2_ip


이 헤더는 클라이언트의 IP 주소(client_ip)와 요청이 거친 프록시 서버의 IP 주소(proxy1_ip, proxy2_ip)를 포함하고 있습니다.

AWS에서는 Elastic Load Balancer와 같은 로드 밸런싱 서비스를 사용할 때,

X-Forwarded-For 헤더를 사용하여 클라이언트의 원래 IP 주소를 전달할 수 있습니다.

그러면 백엔드 서버는 이 헤더를 사용하여 클라이언트의 실제 IP 주소를 확인할 수 있습니다.

### Apache httpd Web Server 에서 XFF 사용 방법

아파치 웹 서버에서는 LogFormat 지시자나 접근 권한 체크 시 Remote Address 를 사용하는데 앞에 Reverse Proxy 가 있다면
의도한대로 동작하지 않으므로 XFF 헤더를 사용하도록 수정해야 합니다.

☞로그포맷
기본 로그포맷은 %h 로 리모트 Address 를 사용하므로 다음과 같이 %{X-Forwarded-For}i 를 사용하도록 수정합니다.


##수정 전
LogFormat “%h %l %u %t \”%r\” %>s %b” common

## 수정 후
LogFormat “%{X-Forwarded-For}i %l %u %t \”%r\” %>s %b” common

☞접근제어(ACL)
ACL 이 필요할 경우 다음과 같이 전역 변수를 설정하고 ACL 이 필요한 곳에서 이 변수를 사용하는 게 편리합니다.


SetEnvIf X-Forwarded-For ^192\.168\.1\. admin_1
SetEnvIf X-Forwarded-For ^192\.168\.2\. admin_2

<Files wp-config.php>
order Deny,Allow
deny from all
</Files>
## 워드프레스 로그인은 내부 IP 에서만 허용
<Files ~ “^wp-login.php”>
Order Deny,Allow
Deny from all
## 내부 IP 만 관리자 로그인 허용
Allow From env=admin_1
Allow From env=admin_2
</Files>

### nginx httpd Web Server 에서 XFF 사용 방법

nginx 는 –with-http_realip_module 옵션을 주고 컴파일해야 실제 ip 를 얻어올 수 있습니다.

먼저 해당 설정에는 http_realip_module 이 필요하므로 nginx -v 명령어로 지원 여부를 확인 후, http_realip_module이 없다면 –with-http_realip_module 옵션 을 주고 Nginx 를 다시 컴파일 합니다.

그리고 nginx.conf 파일을 아래와 같이 설정합니다.


http {
set_real_ip_from 127.0.0.1; # proxy/L4 서버 IP
real_ip_header X-Forwarded-For; #proxy/L4 가 실제 IP 를 설정하는 HTTP Header

## Reference

https://hwan-shell.tistory.com/381

http://blog.plura.io/?p=6597