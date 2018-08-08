---
title: "xcode Use of undeclared type 에러"
categories: ios
excerpt: "Use of undeclared type"
---

## Xcode "Use of undeclared type" 에러 해결법

![](/images/2018-08-07-post1/1.png?raw=true")

프로젝트 빌드는 되는데 계속해서 오류가 발생하는 경우가 있다.

실행이 잘 되기 때문에 그냥 냅두고 계속 진행했지만 자동완성이 먹히지 않고 거슬려서 이유를 찾아보았다.

원인은 Build Phases에 해당 소스가 추가되지 않았기 때문이었다.

프로젝트 타겟 - Build Phases 에서 +버튼을 눌러 해당 소스를 추가해주면 해결된다.

![](/images/2018-08-07-post1/2.png?raw=true")

실행하는데 문제가 없었기 때문에 원인을 찾기 힘들었다...

타겟에 추가가 되지 않았던 이유는 다른 프로젝트에 있는 소스파일을 가져오면서 제대로 설정되지 않았던 것 같다.