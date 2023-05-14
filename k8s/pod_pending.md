# pod 상태가 Pending 으로 지속되는 경우

pod 를 띄웠는데 일정 시간이 지났는데도 pending 상태가 지속되는 경우가 있다.

```
k get pods

NAME                                        READY   STATUS              RESTARTS         AGE
pod/activemq-pod-6fc94fc8ff-rn86k           1/1     Running             0                21h
pod/analysis-59cf7cf8b9-8mz8p               0/1     Pending             0                67s
```

이 경우에는 describe 옵션을 통해 pending 상태의 원인을 파악할 수 있다.

```
k describe pod [pod name]

[ 생략 ]

Events:
  Type     Reason             Age    From                Message
  ----     ------             ----   ----                -------
  Warning  FailedScheduling   2m11s  default-scheduler   0/1 nodes are available: 1 Insufficient memory. preemption: 0/1 nodes are available: 1 No preemption victims found for incoming pod.
```

위의 경우 메모리가 부족해 pod 를 실행시키지 못했다는 에러 로그를 출력하고 있다.


## Reference

https://www.sysnet.pe.kr/2/0/12941
