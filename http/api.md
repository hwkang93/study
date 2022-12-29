# API (Application Programming Interface)

> 어플리케이션을 만들기 위해 제공하는 인터페이스 모음


## API 를 사용하는 이유

#### 보유하고 있지 않은 데이터 사용

개인적으로 생각하는 API 의 가장 큰 가치는, 서비스하는 어플리케이션이 보유하고 있지 않은 데이터를 API 를 통해 조회할 수 있다는 점이라고 생각한다.
사용자에게 제공할 데이터를 별도의 구축 없이 API 만을 통해 필요한 데이터를 조회하고 가공하여 새로운 가치를 만들어 낼 수 있다.

#### 비용 절감

위의 이유에서 파생된 이유이다. API 서비스를 통해 원하는 데이터를 제공받음으로써, 별도의 데이터 구축을 하지 않아도 된다.
가치 있는 데이터일수록 구축하는 데 시간과 비용 노력이 많이 필요한데, 이러한 작업들을 하지 않아도 되니 금전적인 비용 이외에도 시간적 비용, 
공간적 비용 또한 절감할 수 있다.

#### 운영 간소화

데이터를 수집, 갱신, 운영관리에 필요한 작업이 많이 간소화 될 수 있다.

## API 작동 방식

API 이 작동하는 방식은 API 가 생성된 시기와 이유에 따라 네 가지 방식으로 작동할 수 있다.

#### SOAP API

단순 객체 접근 프로토콜을 사용한다. 클라이언트와 서버는 XML 을 사용하여 메시지를 교환하며, 과거에 많이 사용되었던 API 작동 방식이다.
현재는 거의 사용되지 않고 있으며 유연성이 떨어지는 방식이다.

#### RPC API

원격 프로시저 호출 방식. 클라이언트가 서버에서 함수나 프로시저를 완료하면 서버가 출력을 클라이언트로 다시 전송하는 방식

#### WebSocket API

JSON 객체를 사용하여 데이터를 전달하는 최신 웹 API 작동 방식이다. WebSocket API 는 클라이언트 서버 간의 양방향 통신을 지원하며
서버가 연결된 클라이언트에 콜백 메시지를 전달할 수 있다. 어떻게 보면 REST API 보다 효율적일 수 있다.

#### REST API

오늘날 웹에서 볼 수 있는 가장 많이 사용되고 유연한 API 이다. 클라이언트가 서버에 요청을 보내면 서버는 요청에 맞는 결과를 클라이언트에 반환한다.
REST API 는 서버가 클라이언트의 데이터를 저장하지 않는 무상태를 보장하기 때문에 확장 등에 용이하다.


## 마치며

글 작성일을 기준으로 사내 API 를 고도화하고, 유지보수 및 납품을 1년 째가 다 되어 가고 있다.
내년에 신입사원 분들이 입사하시면서 API 에 대한 사내 교육을 진행하게 되었는데 지금 내가 운영하고 있는 API 를 왜 사용자들이 사용하는지
어떠한 장점이 있으며 이러한 장점을 위해 나는 무엇을 해야하는지 고민하다가 API 에 대해 정리를 하게 되었다.
API 를 사용함으로 얻을 수 있는 가치에 대해 다시 한 번 생각해보고 API 를 사용하는 사람들이게 그 가치를 온전히 제공하고 싶다.

## Reference

https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-websocket-api-overview.html?pg=wianapi&cta=websocketapi

https://www.tibco.com/ko/reference-center/what-is-an-api-application-program-interface