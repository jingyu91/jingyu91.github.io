---
title: "App Life Cycle"
category: ios
---

# App Life Cycle



## App Status

![appstatus](/images/ios/appstatus.png)

iOS 에서 앱은 간단하게 3 가지 실행 모드와 5 가지의 상태로 구분이 가능하며 항상 하나의 상태를 가지고 있습니다.

- Not Running : 앱이 시작되지 않았거나 실행중이였다가 종료된 상태.
  - 실행되지 않는 모드와 상태를 모두 의미합니다.
- Foreground
  - Active : 앱이 실행중이고 이벤트를 받는 상태. 일반적인 앱의 상태.
  - Inactive : 실행중이지만 아무런 이벤트를 받지 않는 상태.
- Background
  - Running : 앱이 백그라운드에서 코드가 실행되는 상태.
  - Suspend : 코드가 실행되지 않는 상태. low memory일때 os가 메모리에서 제거한다.





## The Structure of an App

![applifecycle](/images/ios/applifecycle.png)



### UIApplication 

iOS 앱 실행에서 전반적으로 제어하는 중심부이다.

- 앱이 실행되면 UIApplicationMain(::::)을 호출하여 **싱글톤** 방식으로 UIApplication객체를 생성하고 응용 프로그램 개체와 응용 프로그램 위임을 만들고 이벤트주기를 설정한다.

- window의 리스트를 유지 및 관리하여 view에 접근 할 수 있다.
- event loop를 관리하고 앱 전환이나 푸시 노티피케이션 같은 특별한 이벤트를 델리게이트로 위임시킨다.
- UIApplication 클래스는 **UIApplicationDelegate** 프로토콜을 준수하고 일부 프로토콜 메서드를 구현한다.
  -> 앱의 시작, 메모리 부족, 알림, URL처리 등의 상황에 대한 메소드 구현하여 앱의 동작을 관리.



### App delegate

UIApplication과 상호작용하며 앱의 전환이나 이벤트루프와 초기화를 처리한다.

앱이 시작하는 시간은 중요한 시간이다. 앱에서 필요한 데이터 및 자료구조를 설정하고 인터페이스를 조작을 해야한다.

* 앱의 시작 코드가 들어있다.
* 앱의 상태 변화에 대한 응답.
* 앱 외부 알림에 대한 응답.
* 상태 보존 및 복원에 대한 응답.



### UIWindow

뷰를 표시 하는 좌표를 설정하고 뷰컨트롤러와 함께 이벤트와 다양한 작업을 처리한다.

key window로 설정하여 화면에 표시한다.

StoryBoard를 사용하면 Xcode에서 자동으로 window를 포함한다. 하지만 storyboard없이 구성할경우 window를 직접 만들어서 사용해야한다.

```swift
var window = UIWindow(frame: UIScreen.main.bounds)
let vc = myViewController()
window?.rootViewController = vc
window?.makeKeyAndVisible() // key window로 설정
```



- 윈도위의 z축 변경으로 다른 윈도우의 가시성 처리.
- 키보드 이벤트 처리. 
  Notifications - keyboardWillShowNotification같은 메서드를 제공하여 키보드 이벤트를 처리.
- 윈도우의 좌표 변경.
- 윈도우의 루트 뷰 컨트롤러 변경.
- 윈도우가 표시되는 화면 변경. key window의 변경 및 확인하는 메소드 제공.



### UIScreen

하드웨어에 기반한 디스플레이 속성을 가진 객체

해당 기기의 width나 height 등의 값을 얻을때 쓸 수 있다.

```swift
UIScreen.main.bounds.width
```





## Main run loop

![mainrunloop](/images/ios/mainrunloop.png)

전반적인 동작은 유저의 조작이 os를 통해 런루프로 전달되고 앱 실행시 **UIApplication** 객체가 설정한대로 이벤트를 처리하는 것이다. UIApplication 객체는 이벤트를 수신하여 수행해야 할 작업을 결정하는 첫 번째 객체이다.

1. 유저의 조작할때 OS에서 이벤트를 생성한다.
2. UIKit에 설정된 특수 Port를 통해 앱에 전달된다.
3. 전달된 이벤트는 Event큐에 쌓이고 Main run loop에 하나씩 전달된다.
4. 터치 이벤트는 대개 메인 윈도우 객체에 전달되고, 터치는 터치가 발생한 뷰에 디스패치한다. 다른 이벤트는 다양한 앱 객체를 통해 약간 다른 경로를 취할 수 있다.



​    

   



> 출처

https://developer.apple.com/library/archive/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/TheAppLifeCycle/TheAppLifeCycle.html#//apple_ref/doc/uid/TP40007072-CH2-SW1