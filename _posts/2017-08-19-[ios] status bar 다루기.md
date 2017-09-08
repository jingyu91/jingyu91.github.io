## status bar

* status bar color
```
let statusBar = UIView(frame: CGRect(x: 0, y: 0, width: UIScreen.main.bounds.width, height: 20))
        statusBar.backgroundColor = .black // 원하는 색상 지정
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

* status bar text color
text 색상은 white와 black 두 종류로만 바꿀 수 있다.
1. info.plist 수정

![](/images/post1/plist.png")

info.plist에 View controller-based status bar appearance : NO 속성을 추가한다.

2. AppDelegate 수정

AppDelegate - didFinishLaunchingWithOptions메서드 안에 다음 소스를 추가한다.
`UIApplication.shared.statusBarStyle = .lightContent` // white color
or
`UIApplication.shared.statusBarStyle = .default` // black color
