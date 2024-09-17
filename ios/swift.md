
# AEP Mobile SDK implementation for iOS Swift
In this page you can find a step by step guide to implement the Adobe Experience Platform Mobile SDK to interact with Adobe Experience Cloud solutions through Edge Network.

Development environment: iOS Swift

Follow these steps:
1. [setup your environment](#1-setup) with the required dependencies;
2. [configure](#2-implement-life-cycle-metrics) lifeCycle metrics tracking;
3. [track screen views](#3-send-data-to-edge-network);

**IMPORTANT:** the code snippets provided in the paragraphs 3 and 4 are just generic examples. The code snippets required to fulfill the tracking requirements are provided in dedicate pages.

# 1. Setup
## 1.1 Add the dependencies:
```swift
use_frameworks!
pod 'AEPEdge', '~> 4.0'
pod 'AEPEdgeConsent', '~> 4.0'
pod 'AEPAssurance', '~> 4.0'
pod 'AEPEdgeIdentity', '~> 4.0'
pod 'AEPCore', '~> 4.0'
pod 'AEPIdentity', '~> 4.0'
pod 'AEPSignal', '~>4.0'
pod 'AEPLifecycle', '~>4.0'
pod 'AEPUserProfile', '~> 4.0'

```

## 1.2 Add Initialization Code (Sample):
```swift
import AEPCore
import AEPEdge
import AEPEdgeConsent
import AEPAssurance
import AEPEdgeIdentity
import AEPIdentity
import AEPLifecycle
import AEPSignal
import AEPServices
import AEPUserProfile
```   
    
In Xcode, find your didFinishLaunchingWithOptions and add following code to configure the environment and register the required frameworks with Mobile Core:

For Development/Staging/Production:
```swift
let appState = application.applicationState
let extensions = [
      Edge.self,
      Consent.self,
      Assurance.self,
      AEPEdgeIdentity.Identity.self,
      AEPIdentity.Identity.self,
      Lifecycle.self,
      Signal.self,
      UserProfile.self
]
MobileCore.registerExtensions(
  extensions,
  {
    MobileCore.configureWith(appId: "[Launch Environment ID]")
    if appState != .background {
      MobileCore.lifecycleStart(additionalContextData: ["contextDataKey": "contextDataVal"])
    }
  })
```   

> [Launch Environment ID] needs to be substitued with the values reported [here](/custom-data.md).


## 1.3	Enable Debug Logging
> Optional but **recommended** for development build only

Debug logging helps you ensure that the SDK is working as intended. Below is a table that explains the levels of logging available and when they may be used. 
|Log Level       | Description                   |
|----------------|-------------------------------|
|Error			|This log level details unrecoverable errors caused during SDK implementation.           |
|Warning        |In addition to detail from Error log level, Warning provides error information during SDK integration. This log level may indicate that a request has been made to the SDK, but the SDK may be unable to perform the requested task. For example, this may be used when catching an unexpected but recoverable exception and printing its message.            |
|Debug         |In addition to detail from Warning log level, Debug also provides high-level information on how the SDK processes network requests/responses data.|
|Trace			|In addition to detail from the Debug level, Trace provides detailed, low-level information into how SDK processes database interactions and SDK events.|

Note that using Debug or Trace log levels may cause performance or security concerns. It is recommended that you use only Error or Warning log levels in production applications (if planning to enable in production build).

To enable debug logging, add the below lines of code (uncomment any levels you want added):
```swift
//MobileCore.setLogLevel(.trace)
//MobileCore.setLogLevel(.debug)
//MobileCore.setLogLevel(.warning)
//MobileCore.setLogLevel(.error)
```

## 1.4	Register AEPAssurance with Mobile Core

### 1.4.1 Implement AEP Assurance session start APIs
The **startSession** API needs to be called to begin an Adobe Experience Platform Assurance session. When called, SDK displays a PIN authentication overlay to begin a session.
```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey: Any] = [:]) -> Bool {
    Assurance.startSession(url: url)
    return true
}
```
In iOS 13 and later, for a scene-based application, use the `UISceneDelegate`'s `scene(_:openURLContexts:)` method as follows:
```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
    // Called when the app in background is opened with a deep link.
    if let deepLinkURL = URLContexts.first?.url {
        Assurance.startSession(url: deepLinkURL)
    }
}

func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
    // Called when the app launches with the deep link
    if let deepLinkURL = connectionOptions.urlContexts.first?.url {
        Assurance.startSession(url: deepLinkURL)
    }
}
```
# 2. Implement Life Cycle Metrics
This section describes the steps to implement the lifecycle metrics for Adobe Analytics on your native app. After you enable lifecycle, each time your app is launched, a single hit is sent to measure launches, upgrades, sessions, engaged users, and many other Lifecycle Metrics.
## Implementation instructions
1. Start Lifecycle data collection by calling lifecycleStart: in your app's application:didFinishLaunchingWithOptions: delegate method.

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
  let appState = application.applicationState;                        
  
  // ADOBE MOBILE SDK CODE
  MobileCore.registerExtensions(extensions, {
    if appState != .background {
      // only start lifecycle if the application is not in the background
      MobileCore.lifecycleStart(additionalContextData: [:])
    }
  // END ADOBE MOBILE SDK CODE
 
  })
```

2. When launched, if your app is resuming from a backgrounded state, iOS might call your applicationWillEnterForeground: delegate method. You also need to call lifecycleStart:, but this time you do not need all of the supporting code that you used in application:didFinishLaunchingWithOptions:

Code:
```swift
func applicationWillEnterForeground(_ application: UIApplication) {    
    MobileCore.lifecycleStart(additionalContextData: [:])
}
```
4. When the app enters the background, pause Lifecycle data collection from your app's applicationDidEnterBackground: delegate method:
Code:
```swift
func applicationDidEnterBackground(_ application: UIApplication) {
    MobileCore.lifecyclePause()
}
```

# 3. Send data to Edge network
To implement the tracking, we need to use **Edge.sendEvent()** call, passing two objects:
- customData
- xdmData

The content and structure of the objects are specified in the dedicated section for [trackScreen](/ios/trackScreen) or [trackAction](/ios/trackAction).

```swift

var xdmData : [String: Any] = []
var customData : [String: Any] = []

// TRACKING DATA
// Include here the content of the 'customData' and 'xdmData' objects, as per specification.

//Create an Experience Event
let experienceEvent = ExperienceEvent(xdm: xdmData, data: customData)

//Send the Experience Event to Edge Network
Edge.sendEvent(experienceEvent: experienceEvent)
```
