## status bar

ios에서 상태바를 커스터마이징 가능한 부분은 상태바의 백그라운드 색상변경(아무색이나 가능)과 상태바 안의 텍스트 색상변경(하얀색 or 검정색으로 제한)
이다. 

글자색도 변경해보고 싶어지만 애플가이드라인에서는 밝은 배경일때 검은색 텍스트 / 어두운 배경일때 하얀 텍스트를 쓰라고 권장하고 있는 부분이다.
(알록달록한 색상은 불가능함)

### 상태바 백그라운드 색상 변경하기

상태바 백그라운드 색상은 뷰를 만들어서 addSubView 하는 방법과 "statusBar" key로 상태바를 get해서 바꾸는 방법이 있다.
  

```
let statusBar = UIView(frame: CGRect(x: 0, y: 0, width: UIScreen.main.bounds.width, height: 20))
        statusBar.backgroundColor = .green // 원하는 색상 지정
        window?.rootViewController?.view.addSubview(statusBar)
```

or

```
extension UIApplication {
    var statusBarView: UIView? {
        return value(forKey: "statusBar") as? UIView
    }
}

// insert code in AppDelegate
UIApplication.shared.statusBarView?.backgroundColor = .black

```

상태바 백그라운드 색상변경은 비교적 간단하니 이정도로 넘어가고 텍스트 변경을 해보자.

텍스트 변경은 잘못 접근하면 잘 먹히지가 않아서 정리를 제대로 해둬야겠다고 생각했다. 

### 상태바 텍스트 변경하기

상태바를 원하는대로 바꾸기 위해서는 info.plist의 속성설정에 따라 두가지 종류로 나눌 수 있다

![](/images/post1/plist.png?raw=true")

바로 이 속성을 NO로 지정하느냐 YES로 지정하느냐에 따라 설정하는 방법이 다르다.

--- 

* case1. View controller-based status bar appearance = NO 

AppDelegate - didFinishLaunchingWithOptions메서드 안에 다음 소스를 추가한다.

```UIApplication.shared.statusBarStyle = .lightContent // white color```

or

```UIApplication.shared.statusBarStyle = .default // black color```

이렇게 설정을 해두면 모든 뷰컨트롤러에서 같게 세팅이 된다.

info.plist에서 속성을 NO로 지정하지 않았다면 아무리 설정해봐도 먹히지 않으니
바뀌지 않는다면 plist를 다시 확인해보자. 

---

* case2. View controller-based status bar appearance = YES

이 방식은 모든 뷰컨트롤러마다 스타일을 지정해 주어야 한다.

스타일을 지정하는 방법은 이전과 다르게 UIViewController의

```
var preferredStatusBarStyle: UIStatusBarStyle

```

속성을 오버라이드 해서 바꾸는 방법으로 접근해야한다. 

모든 ViewController가 상속받는 base ViewController가 있다면 여기에 설정해 놓는 것이 수월하다.

~~~
    var statusBarStyle: UIStatusBarStyle = .lightContent { didSet { setNeedsStatusBarAppearanceUpdate() } }
    
    override var preferredStatusBarStyle: UIStatusBarStyle {
        return statusBarStyle
    }
~~~

Navigation 뷰컨을 쓴다면 확장해서 설정한다.

```
extension UINavigationController {
    
    override open var preferredStatusBarStyle: UIStatusBarStyle {
        return .lightContent
    }
}

```