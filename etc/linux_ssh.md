# Linux 터미널에서 SSH 접속하기

## 접속 명령어

### 22번포트(기본값)을 사용하는 경우

```shell
ssh 사용자명@도메인(또는 IP주소)
```

### 사용자 지정 포트를 사용하는 경우

```shell
ssh -p 10085 사용자명@도메인(또는 IP주소)
```



## ssh 인증 키 생성 및 서버에 등록하기

서버에 비밀번호가 걸려있는 경우 ssh 명령어로 서버에 접근할 때마다 비밀번호를 쳐야 하는 번거로움이 있다.
이 경우 ssh key 를 생성하고 서버에 등록해두면 접근할 때마다 비밀번호를 쳐야 하는 번거로움을 줄일 수 있다.

방법은 ssh-keygen 으로 공개키/비밀키 한 쌍을 생성하고 공개키 내용을 서버에 저장하면 된다.

다음은 클라이언트에서 인증키를 생성하는 방법이다

```
$ ssh-keygen -t rsa -C "내 아이디"
Enter file in which to save the key (/home/user/.ssh/id_rsa): /home/user/.ssh/my_ssh_key # 키이름을 넣음
Enter passphrase (empty for no passphrase): ********** # 최초 등록시 사용할 비밀문구를 입력함
```

- -t : 키의 타입이 rsa인지 dsa인지 정하는 플래그
- -C : 코멘트

위와 같이 입력하면 my_ssh_key 와 my_ssh_key.pub 파일이 생성되는데, *.pub 파일이 공개키다.

다음은 my_ssh_key.pub 파일을 열어 공개키를 복사하는 방법이다.

```
cat /home/user/.ssh/my_ssh_key.pub
```

위 명령어를 입력하면 터미널 상에 공개키 내용이 출력된다. 해당 내용을 복사해두자. 
이제 이 내용을 서버에 접속해서 ~/.ssh/authorized_keys 에 공개키를 추가해준다. 다음은 해당 서버에서 입력할 명령어다.

```
1. vi ~/.ssh/authorized_keys
2. i 키를 눌러 파일을 읽기모드에서 쓰기모드로 변경한다.
3. 복사한 내용을 붙여넣기 한다
4. esc 키를 눌러 쓰기모드를 종료한다.
5. :wq! 명령어를 입력하면 저장 후 파일을 닫는다. 
```

이제 비밀번호 없이 인증키로 로그인이 가능하다.

## Reference

https://edykim.com/ko/post/creating-an-ssh-authentication-key-and-registering-with-the-server/