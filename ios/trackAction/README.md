# Tracking action views
Every time users interact with the app and a particular action needs to be tracked, use the "Edge.sendEvent" method, as [specified here](/ios#3-send-data-to-edge-network), with the custom data specified below.
the custom data specified below.
Add the events.list.\<actionName\> needed only, not the whole list. Please also notice that *pageView* events needs to be cutoff from this sendEvent method.

The string with angle brackets (e.g. _\<language\>_) are placeholder. Here you can find [the details of each parameter](/custom-data.md).

To implement action tracking, we need to use “Edge.sendEvent()” call with event type as ”web.webInteraction.linkClicks” upon clicking a link or button in your application.
There are three required variables:
- web.webInteraction.name
- web.webInteraction.type
- web.webInteraction.linkClicks.value
The link type can be one of three values:
- other: A custom link
- download: A download link
- exit: An exit link

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
    "errorCode": "<errorCode>",
    
  ],
   "events": [
		"list": [
			"linkAction": [
				"value": "1"
			],
   		]
  ],
... // add paylod specific to the event
]
```


### Mandatory on every event
```swift
var xdm: [String: Any] = [:]
xdmData["eventType"] = "web.webinteraction.linkClicks"
xdmData["web"] = [
  "webInteraction": [
    "linkClicks": ["value": "1"],
    "name": "<link name>",
	"URL": "<url>", // The URL of the link
    "type": "other" // values: other, download, exit
  ]
]
```
