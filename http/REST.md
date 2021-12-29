# REST(RESTful)

REST 는 Representational State Transfer 의 약자로 프로토콜이나 표준이 아닌 아키텍처 원칙 세트이다.

> 프로토콜, 표준이 아니기 때문에 REST 를 구현하는 방식에는 정답이 존재하지 않는다.

RESTful API 를 통해 요청이 수행될 때 RESTful API 는 리소스 상태에 대한 표현을 요청자에게 전송한다.

이 정보 또는 표현은 HTTP : JSON(Javascript Object Notation), HTML, XLT 또는 일반 텍스트를 통해 몇 가지 형식으로 전송된다.

## REST API 의 기준

- **클라이언트 - 서버 아키텍처**
  - 클라이언트, 서버 및 리소스로 구성되어있으며, 요청이 HTTP 를 통해 관리된다.

- **무상태(Stateless) 클라이언트-서버 커뮤니케이션**
  - 요청 간에 클라이언트 정보가 저장되지 않으며, 각 요청이 분리되어 있고 서로 연결되지 않는다.

- **캐시가능(Cacheable)**
  - 클라이언트-서버 간의 상호 작용을 간소화하는 캐시 가능 데이터

- **정보가 표준 형식으로 전송되도록 하기 위한 구성 요소 간 통합 인터페이스**
  - 요청된 리소스가 식별 가능하며 클라이언트에 전송된 표현과 분리되어야 한다.
  - 수신한 표현을 통해 클라이언트가 리소스를 조작할 수 있어야 한다.
    (이렇게 할 수 있는 정보가 표현에 포함되어 있기 때문)
  - 클라이언트에 반환되는 자기 기술적(self-description) 메시지에 클라이언트가 정보를 어떻게 처리해야 할지 설명하는 정보가 충분히 포함되어 있어야 한다.
  - 하이퍼미디어 : 클라이언트가 리소스에 액세스한 후 하이퍼링크를 사용해 현재 수행 가능한 기타 모든 작업을 찾을 수 있어야 한다.

- 요청된 정보를 검색하는 데 관련된 서버(보안, 로드 밸런싱 등을 담당)의 각 유형을 클라이언트가 볼 수 없는 계층 구조로 체계화하는 **계층화된 시스템**

- **코드 온디맨드(선택 사항)**
  - 요청을 받으면 서버에서 클라이언트로 실행 가능한 코드를 전송하여 클라이언트 기능을 확장할 수 있는 기능
- 
































# Reference

[레드햇 - REST](https://www.redhat.com/ko/topics/api/what-is-a-rest-api)