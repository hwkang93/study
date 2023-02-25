# X-Forwarded-For(XFF)

-Forwarded-For(XFF) 헤더는 HTTP 요청 헤더 중 하나로, 클라이언트의 IP 주소를 식별하는 데 사용됩니다.

주로 프록시 서버나 로드 밸런서를 사용할 때 클라이언트의 원래 IP 주소를 전달하기 위해 사용됩니다.



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