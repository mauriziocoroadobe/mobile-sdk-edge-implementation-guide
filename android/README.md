# AEP Mobile SDK implementation for Android
In this page you can find a step by step guide to implement the Adobe Experience Platform Mobile SDK to interact with Adobe Experience Cloud solutions through Edge Network.

Development environment: Android.

Follow these steps:
1. [setup your environment](#1-setup) with the required dependencies;
2. [configure](#2-implement-life-cycle-metrics) lifeCycle metrics tracking;
3. [send data to Edge network](#3-send-data-to-edge-network);

# 1. Setup
Navigate to your app project. Adobe Experience Platform SDK for Android supports Android 4.0 (API 14) or later.

## 1.1 App permission
Ensure App Permissions are set: 
- The SDK requires standard network connection permissions in your manifest to send data, collect cellular provider and record offline tracking calls.
- To add these permissions, add the following lines to your AndroidManifest.xml file, which is located in the application project directory.
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```
## 1.2 Initialize SDK 

We'll use Gradle to manage dependencies.
- Add the dependency to build.gradle:
```java
implementation 'com.adobe.marketing.mobile:edge:2.+'
implementation 'com.adobe.marketing.mobile:edgeconsent:2.+'
implementation 'com.adobe.marketing.mobile:assurance:2.+'
implementation 'com.adobe.marketing.mobile:edgeidentity:2.+'
implementation 'com.adobe.marketing.mobile:core:2.+'
implementation 'com.adobe.marketing.mobile:identity:2.+'
implementation 'com.adobe.marketing.mobile:lifecycle:2.+'
implementation 'com.adobe.marketing.mobile:signal:2.+'
implementation 'com.adobe.marketing.mobile:userprofile:2.+'
```
- Add Initialization Code
```java
import com.adobe.marketing.mobile.AdobeCallback;
import com.adobe.marketing.mobile.Assurance;
import com.adobe.marketing.mobile.Edge;
import com.adobe.marketing.mobile.Extension;
import com.adobe.marketing.mobile.Identity;
import com.adobe.marketing.mobile.Lifecycle;
import com.adobe.marketing.mobile.LoggingMode;
import com.adobe.marketing.mobile.MobileCore;
import com.adobe.marketing.mobile.Signal;
import com.adobe.marketing.mobile.UserProfile;
import com.adobe.marketing.mobile.edge.consent.Consent;
import com.adobe.marketing.mobile.edge.identity.Identity;
import java.util.Arrays;
import java.util.List;
```

- Create MainActivity.java in the app and register the required frameworks as per below:
```java
MobileCore.setApplication(this);
MobileCore.setLogLevel(LoggingMode.DEBUG);
```
	    
> The second line turns on console logging statements (available options are "DEBUG", "VERBOSE", "WARNING", and "ERROR").

```java
com.adobe.marketing.mobile.edge.identity.Identity.registerExtension();
com.adobe.marketing.mobile.Identity.registerExtension();
Consent.registerExtension();
Assurance.registerExtension();
Edge.registerExtension();
UserProfile.registerExtension();
Lifecycle.registerExtension();
Signal.registerExtension();
MobileCore.start(new AdobeCallback () {
	@Override
	public void call(Object o) {
	    MobileCore.configureWithAppID("[Launch Library]");
	}
});
```

> [Launch Environment ID] needs to be substitued with the values reported [here](/custom-data.md).


## 1.3 Verify the AEP SDK imports
1. Save your Android Studio project.
2. Run the app in the Emulator (running Android 4.1 or later) or side-load it in the connected Android device (physical, developer phone, connected to your laptop).
3. Confirm the calls are being made to the Adobe servers in Android Studio Logcat.
4. Below are some of the key calls:
	1. Calls to retrieve the Launch configuration (filter Logcat to adobedtm.com)
	2. Request to the Identity Service (filter Logcat to IdentityExtension)
	3. Request to experience Edge (filter Logcat for AEP Request Event)
5. These calls confirms that AEP SDK has been successfully added to your app/project.
6. Now the tracking calls/solution components can be deployed to the app.

## 1.4	Register AEPAssurance with Mobile Core
```java
public class MobileApp extends Application {
@Override
public void onCreate() {
  super.onCreate();
  MobileCore.setApplication(this);

  try {
     
     // Snippet to register Assurance
     Assurance.registerExtension();  
     // END Snippet to register Assurance
     
     MobileCore.start(new AdobeCallback() {
       @Override
       public void call(final Object o) {
	  MobileCore.configureWithAppID("[Launch Library]");
       }});
  } catch (Exception e) {
     // Log the exception
  }
}
}
```

> In order to test the App with Assurance tool, an App's deeplink should be provided to Adobe team.

# 2. Implement Life Cycle Metrics

Lifecycle metrics/dimensions are valuable, out-of-the-box information about your app user. “Lifecycle metrics” are environment-based metrics and dimensions that can be easily enabled in an app using the AEP SDK. These metrics contain information on the app user's lifecycle such as device information, install or upgrade information, session start and pause times, etc. If you would have followed to above steps of configuring the SDK/Extensions in Adobe Launch and importing the SDK, then you have to complete the below step to enable lifecycle tracking:

1. Add the below Lifecycle code to the main onResume() function in the app, in order to trigger the Lifecycle functions
```java
@Override
public void onResume() {
	MobileCore.setApplication(getApplication());
	MobileCore.lifecycleStart(null);
}
```
2. Use the onPause function to pause the lifecycle data collection.
```java
@Override
public void onPause() {
	MobileCore.lifecyclePause();
}
```
3. To ensure accurate session and crash reporting, these calls must be added to every activity.

#### Best Practices for adding lifecycle tracking

- It is recommended that lifecycle tracking is implemented on all the screens/activities, to ensure an accurate reporting. 
- Setting the application [**MobileCore.setApplication(getApplication())**;] is only necessary on activities that are entry points for your application.
However, setting the application on each Activity has no negative impact and ensures that the SDK always has the necessary reference to your application. We recommend that you call “setApplication” in each of your activities. o You can also use global lifecycle callbacks & and do not need to implement the code for each of the Activity.

# 3. Send data to Edge network

To implement the tracking, we need to use **Edge.sendEvent()** call, passing two objects:
- customData
- xdmData

The content and structure of the objects are specified in [the dedicated section](/android/trackScreen).

```java
HashMap < String, Object > customData = new HashMap < String, Object > ();
Map < String, Object > xdmData = new HashMap < String, Object > ();

// TRACKING DATA
// Include here the content of the 'customData' and 'xdmData' objects, as per specification

ExperienceEvent experienceEvent = new ExperienceEvent.Builder()
  .setXdmSchema(xdmData)
  .setData(customData)
  .build();
Edge.sendEvent(experienceEvent, null);	
```
