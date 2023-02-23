# Docker Container Timezone

도커 개발환경 세팅 후 도커 로그 파일을 보면 한국 시간대가 아닌 UTC 등 다른 시간대를 사용하는 경우가 간혹 있다.
이 경우 한국 시간대로 변경하는 방법을 확인해보자.

```bash
date
```

도커 컨테이너 내에서 사용하는 타임존은 ```date``` 명령어를 통해 확인할 수 있다. 이 때, 

```Wed Feb 23 01:53:49 UTC 2023```

과 같이 UTC 로 확인되는 경우 컨테이너에서 참조하는 시간대가 한국 시간대가 아닌 것을 알 수 있다.

시간대를 변경하는 방법은 다음과 같다.

```bash
ln -sf /usr/share/zoneinfo/대상지역/대상도시 /etc/localtime
```

만약 한국 시간대로 변경하고 싶다면 다음 명령어를 입력해준다.
/usr/share/zoneinfo/ 디렉토리의 권한은 root 이므로 root 계정으로 수정해야 한다.

```bash
ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```

