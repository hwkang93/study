# Git 명령어 정리

- **로그**

got lig

- **설정 정보 조회**

```git config --global --list```

- **저장소 복제**

```git clone [GIT URL]```

- **로컬(작업 디렉토리)에 있는 파일 스테이지에 올리기**

```git add [파일 / 디렉토리 경로]```

```git add .```

> git add -A : 모든 변경 내용을 스테이지에 올림

- **커밋**

```git commit -m "커밋 메시지"```

- **푸쉬**

```git push [저장소명] [브랜치 명]```


- **브랜치 전환하기**

```git checkout [브랜치 명]```

- **unstaged 영역 변경사항 확인**

```git diff```