# Tracking screen views
Every time an app screen is loaded, use the "Edge.sendEvent" method, as [specified here](/android#3-send-data-to-edge-network), with the custom data specified below.

The string with angle brackets (e.g. _\<language\>_) are placeholder. Here you can find [the details of each parameter](/custom-data.md).


Syntax
```java
customData.put("data", new HashMap < String, Object > () {
  {
    put("appName", "<appName>");
    put("appVersion", "<appVersion>");
    put("appId", "<appId>");	  
    put("pageInfo", new HashMap < String, Object > () {
      {
		  put("language", "<language>");
		  put("section", "<section>");
		  put("screenName", "<screenName>");
		};
	});
  };
});
```


### Mandatory on every event 
```java
xdmData.put("eventType", "web.webPageDetails.pageViews");
xdmData.put("web", new HashMap < String, Object > () {
  {
    put("webPageDetails", new HashMap < String, Object > () {
      {
        put("name", "<screenName>");
        put("pageViews", new HashMap < String, Object > () {
          {
            put("value", 1);
          }
        });
      }
    });
  }
});
```
```
