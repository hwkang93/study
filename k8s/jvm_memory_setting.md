# 쿠버네티스 pod 의 메모리 세팅 정리 및 주의점

> 참조 페이지에 있는 글을 내 것으로 만드는 중

k8s deployment yaml 파일 설정이 다음과 비슷한 모양이다.

```yaml
containers:
- name: analysis
  image: b97drbe1.kr.private-ncr.ntruss.com/api_analysis:latest
  imagePullPolicy: Always
  resources:
  limits:
    memory: 32Gi
  requests:
    memory: 4Gi
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
(갑자기 죽어버린 Pod 가 있다면 의심해보자.) 개런티드 QOS 에 대해서 공식 매뉴얼을 참고하길 바란다.


## Reference

https://findstar.pe.kr/2022/07/10/java-application-memory-size-on-container/