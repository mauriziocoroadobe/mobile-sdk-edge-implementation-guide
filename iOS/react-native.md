
# AEP Mobile SDK implementation for iOS with React Native
In this page you can find a step by step guide to implement the Adobe Experience Platform Mobile SDK to interact with Adobe Experience Cloud solutions through Edge Network.

Development environment: iOS Swift

Follow these steps:
1. [setup your environment](#1-setup) with the required dependencies;
2. [configure](#2-implement-life-cycle-metrics) lifeCycle metrics tracking;
3. [track screen views](#3-send-data-to-edge-network);

**IMPORTANT:** the code snippets provided in the paragraphs 3 and 4 are just generic examples. The code snippets required to fulfill the tracking requirements are provided in dedicate pages.

# 1. Setup
## 1.1 Add the dependencies:
```Objective-C
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
Extension registration is mandatory. Attempting to make extension-specific API calls without registering the extension will lead to undefined behavior.

For React Native apps, initialize the SDK using native code in your AppDelegate (iOS).

For Development/Staging/Production:
```Objective-C
@import AEPCore;
@import AEPEdge;
@import AEPEdgeConsent;
@import AEPAssurance;
@import AEPEdgeIdentity;
@import AEPIdentity;
@import AEPLifecycle;
@import AEPSignal;
@import AEPServices;
@import AEPUserProfile;
...
@implementation AppDelegate

-(BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [ACPCore setLogLevel:ACPMobileLogLevelDebug];
    [ACPCore configureWithAppId:@"<your_environment_id_from_Launch>"];
    [ACPUserProfile registerExtension];
    [ACPIdentity registerExtension];
    [ACPLifecycle registerExtension];
    [ACPSignal registerExtension];

    const UIApplicationState appState = application.applicationState;
    [ACPCore start:^{
      // only start lifecycle if the application is not in the background
      if (appState != UIApplicationStateBackground) {
        [ACPCore lifecycleStart:nil];
      }
    }];
    ...
  return YES;
}

@end
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
```javascript
ACPCore.setLogLevel(ACPMobileLogLevel.DEBUG);
//ACPCore.setLogLevel(ACPMobileLogLevel.VERBOSE);
//ACPCore.setLogLevel(ACPMobileLogLevel.WARNING);
//ACPCore.setLogLevel(ACPMobileLogLevel.ERROR);
```

## 1.4	Register AEPAssurance with Mobile Core

### 1.4.1 Implement AEP Assurance session start APIs
When using React Native, register AEP Assurance with Mobile Core in native code as shown on the Android and iOS tabs.

The **startSession** API needs to be called to begin an Adobe Experience Platform Assurance session. When called, SDK displays a PIN authentication overlay to begin a session.
```Objective-C
- (BOOL)application:(UIApplication *)app openURL:(nonnull NSURL *)url options:(nonnull NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options {
    [AEPMobileAssurance startSessionWithUrl:url];
    return true;
}
```
In iOS 13 and later, for a scene-based application, use the `UISceneDelegate`'s `scene(_:openURLContexts:)` method as follows:
```Objective-C
- (void)scene:(UIScene *)scene willConnectToSession:(UISceneSession *)session options:(UISceneConnectionOptions *)connectionOptions {    
    NSURL *deepLinkURL = connectionOptions.URLContexts.allObjects.firstObject.URL;
    [AEPMobileAssurance startSessionWithUrl:deepLinkURL];
}


- (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts {
    [AEPMobileAssurance startSessionWithUrl:URLContexts.allObjects.firstObject.URL];
}
```
# 2. Implement Life Cycle Metrics
This section describes the steps to implement the lifecycle metrics for Adobe Analytics on your native app. After you enable lifecycle, each time your app is launched, a single hit is sent to measure launches, upgrades, sessions, engaged users, and many other Lifecycle Metrics.
## Implementation instructions
1. You should implement Lifecycle metrics in native code. However, Lifecycle's APIs are available in Javascript if it fits your use case.

Starting a Lifecycle event
```javascript
ACPCore.lifecycleStart({"lifecycleStart": "myData"});
```

Pausing a Lifecycle event
```javascript
ACPCore.lifecyclePause();
```


# 3. Send data to Edge network
To implement the tracking, we need to use **Edge.sendEvent()** call, passing two objects:
- customData
- xdmData

The content and structure of the objects are specified in the dedicated section for [trackScreen](/ios/trackScreen) or [trackAction](/ios/trackAction).

```Javascript
examples:
var xdmData  = {"eventType" : "SampleXDMEvent"};
var data  = {"free": "form", "data": "example"};


// TRACKING DATA
// Include here the content of the 'customData' and 'xdmData' objects, as per specification.
let experienceEvent = new ExperienceEvent(xdmData, data);

//Send the Experience Event to Edge Network
Edge.sendEvent(experienceEvent);
```
