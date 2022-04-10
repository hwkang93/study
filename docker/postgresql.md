# Docker 에 PostgreSQL 설치하기 (Synology NAS)

> 루트 권한으로 접속하기

### 도커에 PostgreSQL 이미지 설치하기

```docker pull postgres```


### PostgreSQL 이미지가 정상적으로 설치됐는지 확인하기

```docker images```

### 컨테이너 만들기

#### 명령어

```docker run -d -p 15000:5432 -e POSTGRES_PASSWORD="postgres" --name postgres postgres```

- **docker run** : 도커 이미지를 컨테이너로 생성한다.
- **-p 15000:5432** : 포트 설정. 외부 접근 포트를 15000 으로 내부 포트를 5432 로 설정한다.
- **-e** : 환경변수 설정
- **POSTGRES_PASSWORD="postgres""** : postgres 환경변수 중 비밀번호를 설정하는 부분
- **--name postgres postgres** : 컨테이너 name 설정. 앞의 postgres 는 컨테이너 명 뒤의 postgres 는 참조할 이미지 명


#### 시놀로지 UI 에서 만들기

> docker 및 postgres 가 설치됐다는 가정 하에 작성

1. 패키지 센터 접속 > 설치됨 탭 > Docker 열기
2. 이미지 탭 클릭
3. postgres:latest 더블클릭으로 컨테이너 생성
4. 컨테이너 이름 작성 후 [고급 설정] 클릭
5. 볼륨 > 파일 추가
    - postgres.conf 설정 파일이 저장될 위치를 결정하는 작업
    - 적당한 위치에 postgres.conf 파일을 만들고 파일을 선택해준다.
   > ex) /volume1/docker/postgreSQL/config/postgres.conf
    - 마운트 경로 : /etc/postgresql/postgresql.conf
   
6. 볼륨 > 폴더 추가
   - 컨테이너를 삭제해도 저장된 데이터를 백업하기 위해 설정
   - 적당한 폴더를 만들고 경로를 선택한다. 
   > ex) volume1/docker/postgreSQL/pgdata
   - 마운트 경로 : /var/lib/postgresql/data

7. 포트 설정 > 로컬 포트를 입력한다. 로컬 포트는 외부에서 접근할 때 입력할 포트를 말한다.
8. 환경 > 변수를 추가한다.
    - POSTGRES_USER : postgres
    - POSTGRES_PASSWORD : postgres


root 계정으로 PostgreSQL 접속

```docker exec --user="root" -it postgres "bash"```

```psql -U postgres```



### 오류

만약 컨테이너를 올리는 데 실패한 경우 로그를 확인한다.

```docker logs [Container ID]```



## Reference

[[Docker] Docker 에 PostgreSQL 설치하기 - 천년나무 블로그](https://xeppetto.github.io/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4/WSL-and-Docker/15-Docker-PostGreSQL/)

[시놀로지 NAS의 Docker로 STRAPI 설치하기](https://stories.tistory.com/541)