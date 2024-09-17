# Tracking action views
Every time users interact with the app and a particular action needs to be tracked, use the "Edge.sendEvent" method, as [specified here](/android#3-send-data-to-edge-network), with the custom data specified below.
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
```java
customData.put("data", new HashMap < String, Object > () {
  {
    put("appName", "<appName>");
    put("appVersion", "<appVersion>");
    put("appId", "<appId>");	  
    put("pageInfo", new HashMap < String, Object > () {
                  {
		  put("language", "<language>");
		  put("screenName", "<screenName>");
		  };
    });
   }
}; 
    put("events", new HashMap < String, Object > () {
                {
		put("list", new HashMap < String, Object > () {
                        {	
			put("linkAction", new HashMap < String, Object > () {
                               {
				put("value", "1");
			       }
			});

			};
		});
                }
   });
```


### Mandatory on every event

```java
xdmData.put("eventType", "web.webinteraction.linkClicks");
xdmData.put("web", new HashMap < String, Object > () {
  {
    put("webInteraction", new HashMap < String, Object > () {
      {
        put("name", "<link/button name>");
		put("type", "other");
        put("linkClicks", new HashMap < String, Object > () {
          {
            put("value", 1);
          }
        });
      }
    });
  }
});
```

