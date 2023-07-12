# 쿠버네티스 pod 의 메모리 세팅 정리 및 주의점

Pod 안에서 실행되는 컨테이너가 사용하는 리소스를 제한하지 않으면 Node 의 리소스가 무분별하게 사용될 수 있다. 여러 Pod 가 존재한다면
Qos 클래스 (BestEffort, Burstable, Guaranteed)에 따라 필요한 만큼의 리소스를 얻지 못할 수도 있다. Requests, Limits 를 모두 설정하지 않았다면
리소스 할당 순위에서 뒤로 밀려 실행중인 Pod 가 Kill 당할 수도 있다.

다음은 deploy 템플릿에 request 와 limit 을 설정하는 방법이다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: play-k8s-app
spec:
  containers:
  - name: analysis
    image: b97drbe1.kr.private-ncr.ntruss.com/api_analysis:latest
    imagePullPolicy: Always
    resources:
      requests:
        memory: 4Gi
      limits:
        memory: 32Gi
```

**리소스 할당은 Pod 에 하는 것이 아닌 Pod 에서 실행되는 각 컨테이너에 할당하는 것이다.**

- 현재 리소스 사용량을 보여주는 명령어

```
kubectl top pods
kubectl top pods --containers
kubectl top nodes
```

## pod 의 메모리 설정 시 주의사항

### 1. limit 과 request 를 동일하게 설정하자.

먼저 확인할 것은 resource 설정에서 limit 과 request 를 동일하게 설정하는 것이다. 
처음 resource 설정할 때 실수하기 쉬운 부분 중 하나인데 이 두 값이 같지 않다면 (예를 들어 위의 상황처럼 request 가 4G 이고, limit 이 32G 인 경우) 
의도하지 않게 pod 가 restart 되는 경우가 발생할 수 있다.(에러 메세지는 OOM eviction) 따라서 두 값을 동일하게 설정하기를 권장한다.

#### k8s 의 자원할당 QOS 구성은 총 3가지 클래스가 있다.

- Guaranteed : request = limit
- Burstable : request < limit
- BestEffort : request, limit 설정 없음

위에서 언급한 **request 와 limit 을 같게 설정하라는 의미는 QOS 클래스 중에서 guaranteed 를 사용하라는 의미**인데, 그 이유는 k8s의 메모리 보장방식 때문이다.

클러스터에서 Pod 를 생성할 때 충분한 메모리가 없으면 우선순위가 낮은 pod를 죽여서 메모리를 보장해주는데, 여기서는 OOM Killer 에 대해서 알아야 한다. 
OOM Killer 는 실행 중인 모든 프로세스를 살펴보며 각 프로세스의 메모리 사용량에 따라 OOM 점수를 산출한다. 
OS 에서 메모리가 더 필요하면 점수가 가장 높은 프로세스를 종료시킨다. Pod 도 프로세스이기 때문에 여기에 해당하는데 QOS 클래스에 따라서 OOM Killer 가 참고하는 oom_score_adj 값이 다르다.

| Pod QOS      | oom score adj     |
|--------------|-------------------|
| Guaranteed   | -998              |
| Burstable    | min(max(2, 1000)) |
| BestEffort   | 1000              |

따라서 Pod QOS 클래스를 Guaranteed 로 설정해놓으면 OOM Killer 로 인해서 갑자기 Pod 가 죽는 상황은 발생하지 않는다.


### 2. Heap Memory 설정

위와 같이 설정해놓았더라도 pod 가 메모리를 효율적으로 사용하기 어렵다. 높은 확률로 메모리의 낭비가 발생하기 때문에 Heap 설정을 살펴보아야 한다. 

JVM 애플리케이션에서 OOM Exception 이 발생한다면 다음과 같은 부분이 잘 설정되었는지 고민해보아야 한다.

## Reference

https://findstar.pe.kr/2022/07/10/java-application-memory-size-on-container/

https://effectivesquid.tistory.com/entry/Kubernetes-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C%EC%9D%98-%EC%BB%B4%ED%93%A8%ED%8C%85-%EB%A6%AC%EC%86%8C%EC%8A%A4-%EC%A0%95%EB%B3%B4-%EB%B0%8F-%EC%A0%9C%ED%95%9C