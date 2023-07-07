# Java heap dump example

VM 에 다음 옵션을 추가한다.

```
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=C:\Users\KHW\Desktop
```

쿠버네티스 환경이라면 yaml 파일에 다음과 같이 VM 옵션을 추가할 수 있다.

```yaml
  # ...중략
  containers:
    - name: analysis
      env:
        - name: JAVA_TOOL_OPTIONS
          value: "-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/log/heap.hprof"
```

이제 서버 기동 후, OOME 가 발생하게 되면 .hprof 확장자를 가진 dump 파일이 생성된다.

