---

title: "[WatchOS]Complication"

category: ios

---

# [WatchOS]Complication

Complication은 워치 페이스에서 빠르게 해당 앱을 실행할 수 있는 기능이다..

워치 페이스에서 시간에 따라 데이터를 다르게 표기할 수 있다.

여기서는 단순 이미지만 표기하고, 클릭하였을 때 앱이 켜지고 기능을 수행하는 것 까지 다룬다.



- 표시 타입

  - ModularSmall
  - ModularLarge
  - UtilitarianSmall
  - UtilitarianLarge
  - CircularSmall
  - ExtraLarge
  - Graphic Corner
  - Graphic Bezel
  - Graphic Circular
  - Graphic Rectangular

  

- 하루 50번의 통신 제약

  iOS에서 데이터를 보내 Watch Face에 표시할 때 통신





## How to use?

### 설정

- Complication은 소스단에서 컨트롤러를 설정할 수 없음

- project 설정 - Complication Configuration에서 Data Source Class를 선택

- 설정에서 지원할 타입을 선택하고 image asset을 추가

- 아이폰의 watchface에서 이미지를 표시하기 위해 Bundle 추가
  - https://developer.apple.com/library/archive/releasenotes/WatchKit/AddingComplications/

- 여러 타입을 지원할 경우 이미지가 제대로 표시되지 않는 경우
  - utility, modular, circularSmall 등의 타입은 **알파채널** 이미지만 지원하기 때문에 흰 사각형만 표시될 수 있다.
  - CLKComplicationDataSource 에서 customizing 코드 작성



### 콜백 메서드 구현

ExtensionDelegate에서 Complication을 클릭하여 진입하였을 때 콜백을 받아서 원하는 기능을 수행할 수 있다.

```swift
class ExtensionDelegate: NSObject, WKExtensionDelegate {

    func handleUserActivity(_ userInfo: [AnyHashable : Any]?) {
 			// 이곳에서 화면 이동 등의 기능을 작성    
    }
  
  ...
}
```



## Source

```swift
import WatchKit

class ComplicationController: NSObject, CLKComplicationDataSource {
    func getSupportedTimeTravelDirections(for complication: CLKComplication, withHandler handler: @escaping (CLKComplicationTimeTravelDirections) -> Void) {
        handler([]) // 타임라인 사용 안함
    }
    
    func getCurrentTimelineEntry(for complication: CLKComplication, withHandler handler: @escaping (CLKComplicationTimelineEntry?) -> Void) {
        if let tmp = getTemplate(complication.family) {
            handler(CLKComplicationTimelineEntry.init(date: Date(), complicationTemplate: tmp))
        } else {
            handler(nil)
        }
    }
    
  // placeholder이미지를 표시 (complication을 바꿀때 표시되는 이미지)
    func getLocalizableSampleTemplate(for complication: CLKComplication, withHandler handler: @escaping (CLKComplicationTemplate?) -> Void) {
        handler(getTemplate(complication.family))
    }
    
  // 타입으로 템플릿을 가져오는 함수
    func getTemplate(_ type: CLKComplicationFamily) -> CLKComplicationTemplate? {
        
        switch type {
        case .modularSmall:
            let tmp = CLKComplicationTemplateModularSmallSimpleImage()
            let imageProvider = CLKImageProvider(onePieceImage: UIImage(named: "test")!)
            tmp.imageProvider = imageProvider
            tmp.tintColor = .purple
            return tmp
            
        case .utilitarianSmall:
            let tmp = CLKComplicationTemplateUtilitarianSmallSquare()
            tmp.imageProvider = CLKImageProvider(onePieceImage: UIImage(named: "test")!)
            tmp.tintColor = .purple
            return tmp

        case .graphicCorner:
            let tmp = CLKComplicationTemplateGraphicCornerCircularImage()
            tmp.imageProvider = CLKFullColorImageProvider(fullColorImage: UIImage(named: "Complication/Graphic Circular")!)
            return tmp

        case .circularSmall:
            let tmp = CLKComplicationTemplateCircularSmallSimpleImage()
            let imageProvider = CLKImageProvider(onePieceImage: UIImage(named: "test")!)
            tmp.imageProvider = imageProvider
            tmp.tintColor = .purple
            return tmp

        default:
            return nil
        }
    }
}

```





## Reference

- design guide

   https://developer.apple.com/design/human-interface-guidelines/watchos/app-architecture/complications/



- blog

  https://theswiftdev.com/2016/04/28/clockkit-complications-cheat-sheet/

  https://blog.drkaka.com/apple-watch-complication-templates-f78b75a80bb1
