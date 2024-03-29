# Referer

리퍼러 (헤더명 : Referer) 는 사용자가 어느 경로로 웹사이트에 도달했는지 서버가 파악할 수 있도록 클라이언트가 서버에 보내는 헤더를 말한다.




> 스펠링이 referrer 와 다른데, 그 이유는 RFC 1945 제안 당시의 오타가 그대로 남았기 때문이라고 한다.


NO_REFERRER: 리퍼러 정보를 포함하지 않고 요청을 보낸다.

NO_REFFERER_WHEN_DOWNGRADE: HTTPS에서 HTTP로 요청이 다운그레이드되는 경우에만 리퍼러 정보를 포함하지 않고 요청을 보냅니다.

SAME_ORIGIN: 같은 출처(Origin)에 대해서만 리퍼러 정보를 포함하고 요청을 보냅니다.

STRICT_ORIGIN: 같은 출처(Origin) 및 하위 경로에 대해서만 리퍼러 정보를 포함하고 요청을 보냅니다.

ORIGIN: 리퍼러 정보를 포함하고 요청을 보냅니다.

## Referer 와 Origin

#### Referer

리퍼러는 현재 웹 페이지로 사용자를 이동시킨 이전 페이지의 URL을 나타냅니다. 즉, 사용자가 현재 페이지로 이동하기 전에 어느 페이지에서 링크를 클릭하거나 요청을 보냈는지를 나타내는 URL입니다. 
예를 들어, 사용자가 웹 페이지 A에서 웹 페이지 B로 이동할 때, 웹 페이지 B는 리퍼러로 웹 페이지 A의 URL을 갖게 됩니다. 
리퍼러는 보통 HTTP 헤더에 포함되어 서버로 전송되며, 웹 서버는 이를 이용하여 사용자의 경로 및 유입 경로를 파악하는 데 사용할 수 있습니다.

#### Origin

오리진은 웹 페이지가 로드된 URL의 프로토콜(https, http 등), 호스트(도메인), 그리고 포트 번호를 조합하여 나타낸 보안 관련 개념입니다. 
이는 웹 브라우저에서 웹 페이지 간에 자바스크립트를 통한 데이터 공유 및 보안 정책을 적용하기 위해 사용됩니다. 
예를 들어, 사용자가 웹 페이지 A(https://example.com)에서 웹 페이지 B(https://api.example.com)로 AJAX 요청을 보낼 경우, 웹 페이지 B는 웹 페이지 A와 동일한 오리진을 갖게 됩니다. 
오리진은 웹 보안 및 권한 제어를 위한 중요한 개념으로 사용되며, 동일 출처 정책(Same-Origin Policy) 등의 웹 보안 메커니즘에 사용됩니다.


## Reference

https://granger.tistory.com/27

https://www.w3.org/TR/referrer-policy/