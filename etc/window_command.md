
> 자주 사용하는 Window 명령어 정리

- tasklist
  - 프로세스 목록 조회
  - /V - 자세한 작업 정보 표시 (tasklist /V)

- taskkill /f /im [프로세스 파일 명]
  - 프로세스 파일 명으로 프로세스 죽이기

- taskkill pid [PID] /f
  - PID 가 [PID] 인 프로세스 죽이기

- netstat -a -n -o
  - 현재 실행중인 프로세스 포트, PID 조회

- dir
  - 디렉토리 목록 조회

- D:
  - D 드라이브로 이동

- ls -l | wc -l
  - 현재 폴더의 파일 개수 구하기

- start .
  - 현재 위치 디렉토리 열기
  - ```start ..``` 는 상위 디렉토리 열기

- jar cvf [WAR NAME].war *
  - WAR 파일 만드는 명령어 
  - 소스코드 최상위 디렉토리로 이동 후 아래 명령어를 입력한다.
