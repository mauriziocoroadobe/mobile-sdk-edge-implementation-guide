# Tracking screen views
Every time an app screen is loaded, use the "Edge.sendEvent" method, as [specified here](/ios#3-send-data-to-edge-network), with the custom data specified below.

The string with angle brackets (e.g. _\<language\>_) are placeholder. Here you can find [the details of each parameter](/custom-data.md).


Syntax
```swift
var customData: [String: Any] = [:]
customData = [
  "appName": "<appName>",
  "appVersion": "<appVersion>",
  "appId": "<appId>",
  "pageInfo": [
    "language": "<language>",
    "section": "<section>",
    "screenName": "<screenName>",
    "errorCode": "<errorCode>"
  ]
  ... // add here extra payload when needed
]
```


### Mandatory on every event
```swift
var xdmData: [String: Any] = [:]
xdmData["eventType"] = "web.webPageDetails.pageViews"
xdmData["web"] = [
  "webPageDetails": [
    "pageViews": ["value": "1"],
    "name": "<screenName>",
  ]
]
```
