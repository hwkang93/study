# 두 대 이상의 지오서버를 동기화하는 톰캣에서 발생했던 에러 정리

운영 서버에서 발생한 에러.

맵서버로 지오서버를 두 대를 사용하고 있고, 두 대의 맵서버는 실시간으로 동기화를 진행한다.
동기화 진행을 위해 클러스터 서버로 톰캣을 사용한다.

## 현상

레이어를 발행, 또는 수정하여 다른 맵서버에 동기화를 요청해야 하는 경우, 동기화가 진행되지 않고 서버가 멈추고
클러스터 톰캣에 다음과 같은 에러메시지가 나온다.

```
15:18:02,804 INFO  BaseDestination - Usage(defaulttore:queue://Consumer.d9add864-43d6-4679-a72e-ddda00fea607.VirtualTopic.geoservertore) percentUsage=0%, 
usage=1080745321, limit=1073741824, percentUsageMinDelta=1%;Parent:Usage(defaulttore) percentUsage=100%, usage=1080745321, limit=1073741824, 
percentUsageMinDelta=1%: Persistent store is Full, 100% of 1073741824. Stopping producer (ID:project-li-map2-34515-1650433816897-1:1:2:1) 
to prevent flooding queue://Consumer.d9add864-43d6-4679-a72e-ddda00fea607.VirtualTopic.geoserver. 
See http://activemq.apache.org/producer-flow-control.html for more info (blocking for: 1623s)
```

## 원인

A 맵서버 -> 클러스터 톰캣 -> B 맵서버
로 동기화를 진행하게 되는데, 이 과정에서 메시지 큐가 쌓이게 된다(kahadb 라는 디렉토리에)
메시지 큐가 저장되는 디렉토리의 허용치를 초과해 정상적으로 동기화가 진행되지 않았다.


## 해결방법

- 로그 디렉토리의 가용 용량 설정 수정

1. TOMCAT_DIR/webapps/ROOT/WEB-INF/classes 디렉토리로 접근하여 standalone-broker.properties 파일을 편집기로 실행한다.
2. activemq.broker.systemUsage.tempUsage 의 값을 수정하고 저장한다.

- 로그 파일 삭제

1. TOMCAT_DIR/bin 디렉토리로 이동한다.
2. 로그가 적재되고 있는 kahadb 폴더 내의 로그파일을 삭제한다.

> 백업은 반드시 진행한다.


## 회고

- 확인해야 할 서버들의 로그가 많아 로그 레벨이 ERROR 인 로그 메시지만 집중적으로 확인했다.
왜 이렇게 로그 메시지들이 불친절한지 속으로 궁시렁거렸는데 알고 보니 내가 보고 싶은 로그만 봤던 거였다.

- 팀원들과 함께 해결한 에러라 뭔가 더 뿌듯했다.