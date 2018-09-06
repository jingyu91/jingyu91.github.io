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


—본문 추가

계속해서 새로 생성한 file이 ignored 되는 현상이 발생했다.

분명 .gitignore에는 없는데 왜 자꾸 강제로 ignore되는 것일까?

알아보니 git에서 global한 ignore 설정을 적용시킬 수 있는 .gitignore_global 이라는 파일이 있었다.

위 파일은 다음 명령어로 위치를 찾을 수 있다.

```c
git config --get core.excludesfile
/Users/jingyu/.gitignore_global
```

파일을 열어서 ignore된 line을 수정하면 끝.