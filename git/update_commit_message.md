# Commit 메시지 수정하기

## 1. Commit 이 로컬에 있을 때

### 1-1. 가장 최근의 Commit 수정하기

``` git
git commit --amend
```

```git command --amend``` 명령어를 입력하면 커밋을 수정할 수 있는 창이 뜨는 데, 수정이 완료되면
```esc``` -> ```:wq!``` (저장 + 창닫기) 를 해주면 된다.

> 저장 안하고 그냥 나가기 : ```:q!```

### 1-2. 가장 최근이 아닌 Commit 수정하기

먼저 ```git log``` 를 입력해 커밋 목록을 조회한다.

수정해야 할 커밋을 확인한 후 만일 위에서부터 세 번째 커밋을 수정해야 한다면

```git rebase -i HEAD~3```

위 커맨드를 사용하면 현재 작업중인 브랜치의 가장 최근 commit 3개를 보여주게 된다

그럼 다음과 같은 창이 나온다.

```shell
pick e499d89 Delete CNAME
pick 0c39034 Better README
pick f7fde4a Change the commit message but push the same commit.

# Rebase 9fdb3bd..f7fde4a onto 9fdb3bd
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

이젠 수정하고 싶은 커밋 옆의 pick 이라는 문구를 reword 로 바꿔 주면 된다.

예를 들어 두 번째 커밋과 세 번째 커밋을 수정하려고 한다면 아래와 같이 하면 된다.

```
pick e499d89 Delete CNAME
reword 0c39034 Better README
reword f7fde4a Change the commit message but push the same commit.
```

esc -> :wq 를 통해 커밋 리스트를 저장을 해주고 나면, 두 개의 커밋을 각각 수정할 수 있는 창이 순서대로 띄워진다.

원하는대로 커밋을 수정하고, :wq 를 통해 저장해주면 된다.



## 2. Commit 을 이미 푸쉬해 remote 에 올라갔을 때

커밋이 이미 remote 에 올라간 상황이라면, force 를 통해 수정된 커밋을 강제로 push 해줘야 한다.

github 공식 문서에 따르면 force pushing 을 최대한 사용하지 말라고 한다. push 된 커밋의 로그를 가지고 있던 다른 팀원들이
로그를 수동으로 수정해줘야 하기 때문이다.

> We strongly discourage force pushing, since this changes the history of your repository. 
> If you force push, people who have already cloned your repository will have to manually fix their local history. 
> For more information, see "Recovering from upstream rebase" in the Git manual.

방법은 아래와 같다. 로컬에서 커밋 메시지를 수정하고 아래 커맨드를 실행하면 된다.

``` git
git push --force [브랜치 명]
```

## Reference

https://velog.io/@mayinjanuary/git-%EC%BB%A4%EB%B0%8B-%EB%A9%94%EC%84%B8%EC%A7%80-%EC%88%98%EC%A0%95%ED%95%98%EA%B8%B0-changing-commit-message