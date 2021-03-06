# HTTP Request Method

## GET
- 데이터 조회 시 사용. GET을 사용하는 요청은 오직 데이터를 받기만 한다.
- 멱등을 보장한다.
- 캐싱이 가능하다.
- 데이터 전송 길이에 제한이 있다.
- 쿼리스트링을 통해 파라미터를 서버로 전송한다.
    - KEY=VALUE 형태로 파라미터 전송
    - 파라미터 간에는 & 로 연결
    - 세그먼트(#)도 있음
    
## POST
- 데이터 등록 시 사용
- 파라미터를 Request Body에 담아 전송함.
- 데이터 전송 길이에 제한이 없다.
  > 데이터 전송 길이에 제한이 없기 때문에
  > 조회 요청을 보낼 때 파라미터가 GET 요청에서 허용하는 범위를 넘어갔을 경우 POST 요청을 사용하기도 한다.

- 멱등을 보장하지 않는다.

## DELETE
- 데이터 삭제 시 주로 사용
- 멱등을 보장한다.

> **멱등(Idempotent)**
>
> 같은 요청을 여러 번 진행하더라도 같은 결과를 응답받는 것을 의미

## PUT
- 데이터의 전부를 변경할 때 사용
- 멱등을 보장한다.

## PATCH
- 데이터의 일부를 변경할 때 사용
- 멱등을 보장하지 않는다.

### PUT VS PATCH
**PUT** 이나 **PATCH** 둘 다 데이터의 변경(수정) 이라는 공통의 목적을 가지고 있지만, 
**멱등 여부**와 **데이터의 전체 변경 VS 일부 변경** 이라는 차이점을 가지고 있음

- **멱등 여부**

    - PUT : 멱등을 보장한다.
    - PATCH : 멱등을 보장하지 않는다.

  예를 들어, PATCH는 서버에 요청을 보낼 때마다 100원씩 증가하도록 설계가 가능한다.

  {

    id : 'kang',

    type : 'add',

    value : 100

  }

  이런 요청을 서버에 하는 경우, 멱등을 보장하지 않으므로 PATCH 를 사용하는 것이 바람직하다.

  > 설계에 따라, PATCH를 PUT 과 같은 방식으로 사용함으로써 PATCH 요청에 멱등성을 가지게 할 수도 있다.

- **데이터의 전체 변경 여부**
  - PUT : 전체 변경
  - PATCH : 일부 변경

    이름, 나이, 직업 컬럼을 가진 사용자가 있다고 가정하자. 그리고 그 사용자의 직업이 변경되어 서버에 변경 요청을 보내야 하는 상황이다.
    
    PATCH 요청의 경우 사용자의 이름(키 값이라고 가정) 과 수정이 필요한 직업을 서버에 전송한다. 서버는 클라이언트가 수정을 요청한 컬럼만 갱신한다.
  
    하지만 PUT 요청의 경우 사용자의 이름(key)과 수정이 필요한 직업 정보, 수정이 필요하지 않은 나이 정보도 요청에 포함한다.
    서버는 클라이언트가 요청한 모든 데이터를 갱신한다.


# Reference
