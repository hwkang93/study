# Docker 명령어 정리

### System

- docker 버전 확인

```
$ docker version
```

- docker 실행 환경 확인

```
$ docker system info
$ docker info
```

- docker disk 이용 상황

```
$ docker system df
```

### Image

- docker 이미지 목록 확인

```
$ docker images
$ docker image ls
$ docker image ls -a        -> 모든(중간이미지까지) 이미지를 표시
$ docker image ls --digests -> digest 도 함께 표시
$ docker image ls -q        -> docker id 만 표시 
```

> **docker digest**
> 
> Docker v2 부터 제공하는 도커 이미지의 해시값. sha256 을 베이스로 하며, 해당 이미지의 수정 사항이 있을 경우
> digest 값이 변경된다. 

- docker 이미지 상세정보 확인

```
$ docker image inspect [이미지명/이미지 아이디]
$ docker inpect [이미지명/이미지 아이디] | grep "검색어"     -> 검색어가 포함된 상세 정보만 조회
```

- docker 이미지 다운로드

```
$ docker pull [다운로드 받을 아이템]
$ docker image pull [다운로드 받을 아이템]                       -> :latest 태그 다운로드(디폴트)
$ docker image pull [다운로드 받을 아이템]:[태그명]               -> 명시한 태그의 아이템을 다운로드
$ docker image pull -a [다운로드 받을 아이템]                    -> 아이템의 모든 태그를 다운로드
$ docker image pull gcr.io.tensorflow/[다운로드 받을 아이템]     -> url 로부터 다운로드
```

- docker 이미지 삭제

```
$ docker image rm [이미지명/이미지아이디]
$ docker image rm -f [이미지명/이미지아이디]          -> 이미지 강제 삭제
$ docker image rm --no-prune [이미지명/이미지아이디]  -> 이미지 삭제(중간이미지는 삭제하지 않음)
$ docker image prune                              -> 사용하지 않는 이미지 삭제
$ docker image prune -a                           -> 사용하지 않는 이미지 모두 삭제
$ docker image prune -f                           -> 사용하지 않는 이미지 강제로 삭제
```


> 추가로 옵션을 지정하지 않을 경우 가장 최신 버전의 이미지를 다운로드 받는다. (:latest)
> 
> [다운로드 받을 아이템] : 이미지 / 저장소 등

### Container

- docker 컨테이너 실행

```
$ docker container --name [컨테이너명] -d -p [외부접근포트]:[내부포트] [이미지명]
```

> **상세 설명** 
> 
> --name : 다음에 올 문자열을 컨테이너 명칭으로 정의하겠다.
> 
> -d : detached mode. 컨테이너를 백그라운드에서 실행한다.
> 
> -p : 포트 설정. 다음에 올 문자열을 포트로 설정한다.

- docker 컨테이너 목록 조회

```
$ docker container ls
$ docker container ps
$ docker container ls -a    -> 실행중이지 않은 목록도 함께 조회
$ docker container ps -a    -> 실행중이지 않은 목록도 함께 조회
```

- docker 컨테이너 상세 조회

```
$ docker container inspect [컨테이너명/컨테이너 아이디]
```

- docker 컨테이너 사용 조회(CPU, 메모리 사용량 등)

```
$ docker container stats
$ docker container stats [컨테이너명/컨테이너 아이디]
```

- docker 컨테이너 기동

```
$ docker start [컨테이너명/컨테이너 아이디]
```

- docker 컨테이너 중지

```
$ docker stop [컨테이너명/컨테이너 아이디]
```

- docker 컨테이너 로그 확인

```
docker container logs -f [컨테이너명/컨테이너 아이디]
docker container logs -f --tail 1000 [컨테이너명/컨테이너 아이디]   -> 최근 쌓인 로그 1000줄 조회
```

- docker 컨테이너 포트 확인

```
docker container port [컨테이너명/컨테이너 아이디]
```

- docker 컨테이너 일시 중지

```
docker container pause [컨테이너명/컨테이너 아이디]     -> 해당 컨테이너의 STATUS 가 paused 로 변경됨
docker container unpause [컨테이너명/컨테이너 아이디]   -> 재개
```

- docker 컨테이너 이름 변경

```
docker container rename [올드명] [신규명]
```

- docker 컨테이너 변경 사항 확인

```
docker container diff [컨테이너명/컨테이너아이디]
```

> **변경구분자**
> 
> A : 파일 추가
> D : 파일 삭제
> C : 파일 수정

## Reference

[Docker Docs](https://docs.docker.com/engine/reference)

[Docker 명령어 정리 - 흔적s](https://iamreo.tistory.com/entry/Docker-%EB%AA%85%EB%A0%B9%EC%96%B4-%EC%A0%95%EB%A6%AC)

[[Docker] Docker 에서 이미지의 Digest 에 대한 간단한 고찰 - alice](https://blog.naver.com/alice_k106/221149596996)