---
category: git
---



# 파일을 생성하거나 수정했는데 git 상태변화가 없을때

파일을 생성하고 작업을 했는데 git status에 아무 변화가 없거나 commit할게 없거나 add할게 없다고 나오는 상황이 발생하는 경우가 있다.



이 상황에서 살펴볼 것은 먼저 **.gitignore**에 해당 파일이 등록되어 있는지 확인해 보아야 할 것이다.

커맨드창에 다음과 같이 입력해서 해당 파일을 찾아본다.

```c
vi .gitignore
```



만약 이 방법에 해당하지 않는다면 다음 명령어를 입력해본다.

```c
git status --ignored
```

![gitstatusIgnore](/images/git/gitstatusIgnore.png)



위 사진처럼 ignore된 파일들의 리스트가 나온다.

여기서 해당 파일이 존재한다면 다음 명령어로 강제로 추가해 줄 수 있다.

```c
git add --f [file]
```