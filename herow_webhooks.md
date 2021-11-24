# HEROW Webhooks

The HEROW platform enables you to send different types of events to any HTTP endpoints of your choices. 

In this section, you will discover how to configure each HEROW Webhooks securely, understand what type of payload is available in each webhook and how to use them.

1. Configure HEROW Events

2. Safety procedure to protect your app

3. HEROW webhook types: 
    - Zone Enter
    - Zone Visit
    - Notification Received
    - Notification Opened

<br>

> Note: 
>
> - From the SDK 7 version onward, the HEROW SDKs do not communicate position statements & event's lattitudes & longitudes back to the platform to preserve user's privacy. Instead we use 6 digits [geohashes](https://en.wikipedia.org/wiki/Geohash).

<br>


# Understand HEROW events

## Configuration

1.  Go on your [HEROW](https://my.herow.io/#/login) account

2. Go to "Integrate" section on the left side panel. 

3. Fill in the Name, Destination URL and Trigger. 
<br>

![image](https://user-images.githubusercontent.com/88100049/143237258-fa5aa185-1607-4139-a6ee-449d5eb6e2f1.png)


Whenever events are generated, HEROW will send a POST request containing event and user data to the specified URL.


# Safety procedure to protect your app

### Payload decryption

The payload is formated in JSON and uses Json Web Token (JWT) sign with Apikey (Base64 + HS256) to help you authentify the event origin. 

See here for more informtion ["jti" (JWT ID)](https://tools.ietf.org/html/rfc7519#page-10)

* The issuer is define with herow.webhook.jwt.issuer property
* Id is a random UUID


### how to verify & access the payload ?

<br>

>
>Note: This is a code example


* First, encode and hash with HS256 you apiKey.

```
        byte[] base64secret = Base64.getEncoder().encode(webhook.getApiKey().getBytes(StandardCharsets.UTF_8));
        Key hmacKey = new SecretKeySpec(base64secret,
                SignatureAlgorithm.HS256.getJcaName());
```  

* Parse your response body to JWS.

```
  Jws<Claims> jwt = Jwts.parser().setSigningKey(hmacKey).parseClaimsJws(response.body());
```

* finally, map the jwt claim payload into String and then read json value with ObjectMapper.

```
 ZoneEnterPayload zoneEnterPayloadReceived = new ObjectMapper().readValue(jwt.getBody().get("payload", String.class), ZoneEnterPayload.class);
```

#### Retry mecanism


You can custom the retry with the following properties :

* herow.webhook.tolerence.max-attempts
* herow.webhook.tolerence.wait-duration
* herow.webhook.tolerence.fail-after-max-attempts
* herow.webhook.tolerence.retry-on-exceptions

Example for the send-event-payload
service [WebhookClient#sendSynchroneVisitPayload](webhook/src/main/java/io/herow/event/webhook/client/WebhookClient.java)

```
herow.webhook.tolerence.max-attempts=4
herow.webhook.tolerence.wait-duration=5000
herow.webhook.tolerence.fail-after-max-attempts=true
herow.webhook.tolerence.retry-on-exceptions=javax.ws.rs.ClientErrorException,javax.ws.rs.ServerErrorException
herow.webhook.tolerence.retry-backoff=2000
herow.webhook.tolerence.jitter=0.2
```

* If you want to add another configuration you must add an annotation (
  like [RetryWebhookClientForSendEventPayload](webhook/src/main/java/io/herow/event/webhook/client/annotation/RetryWebhookClientForSendEventPayload.java))

### Slack test

In order to test an incomming webhook, you have to fill-in differents properties :

* herow.webhook.test.slack.enabled **set to true**
* herow.webhook.test.slack.url **set to the url slack webhook**
* herow.webhook.test.slack.url.trigger **set to the url you want to trig.**

In the following example, we enabled the slack test with Herow slack incomming webhook when we put on Herow plateform
interface https://test.event.herow.io :

```
herow.webhook.test.slack.enabled=true
herow.webhook.test.slack.url=https://hooks.slack.com/services/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
herow.webhook.test.slack.url.trigger=https://test.event.herow.io
```

You have 2 possibilities :

* Send the incomming webhook on channel, then set the webhook's URL on the HEROW platform to :
  ${herow.webhook.test.slack.url.trigger}/channel=myChannel
* Send the incomming webhook in a direct message with the Slackbot, then set the webhook's URL on the HEROW platform:
  ${herow.webhook.test.slack.url.trigger}/@MySlackID



# HEROW Webhook types

HEROW offers 4 different types of webhooks. 

## Zone Enter

### Introduction

Based on the HEROW places you import on the platform, your users will start generating zone enter events. 

An event is generated anytime a user is detected entering a zone (constant detection when approaching a geofence) or if its first recorded position statement is alreadyd in the place.

### Subscribe to this event 

In the trigger selection in the platform, select "zone enter".

### Event Frequency

A zone enter event si sent out anytime a user is detected in a new zone. If a user is detected multiple times in the same zone, only one event is sent out.

### Payload format

```Json
{
  "eventDate":1619512887033,
  "id":"ef29f70f-b6bb-4738-aa87-8f49192fd143",
  "type":"ZONE_ENTER",
  "action":"CREATED",
  "user": {
    "phoneId":"45BC1F13-CA38-4C5B-AD0E-DF3A8299CCB5",
    "customId":"thomas",
    "userAgent":"Mozilla/5.0 (iPhone11,8; CPU iPhone OS 14_4_1 like Mac OS X) FxiOS/3.3.0b529",
    "sdkVersion":"6.3.0",
    "appVersion":"3.3.0",
    "company":"appdemo",
    "sdk":"appdemo_herow3"
  },
  "geofence":{
    "placeId":"1dfksx1xkxqf4",
    "placeExternalId":null,
    "placeName":"Avenue de Mâcon",
    "tags":["common"],
    "location":{
      "lat":46.210631780094744,
      "lng":5.218881486288663,
      "confidence":1.0,
      "geohash":"u05wyu2k"},
    "accuracy":65.0,
    "confidence":1.0,
    "distanceToCenter":101.0
  },
  "campaign":null,
  "place":{
    "id":"f75dc1e7da8fe3c9582aa3638e2592b5",
    "name":"zone enter test",
    "location": {
      "lat":22.3,
      "lng":3.2,
      "confidence":1.0,
      "geohash":"5d8he"
    },
    "radius":11.0,
    "tags":["my-tag"]
  }
}
```
## Zone Visit

### Introduction

Similar to the zone enter, the zone visit webhooks trigger in relation with set places. 

When user is detected out of a given place, a zone visit event will trigger. 

>
> Note : the zone visit has one more **duration** property which equals the visit duration in milliseconds. 
>
> Note 2: if the user's device can only access location hours before the leaving the zone (foreground location permission only and app closed when leaving the zone), the zone visit event will trigger the next time the device gets access to locaiton.

### Subscribe to this event 

In the trigger selection in the platform, select "zone visit".

### Event Frequency

Similar to the zone enter event.

### Payload format

```Json
{
  "eventDate":1619512887033,
  "id":"ef29f70f-b6bb-4738-aa87-8f49192fd143",
  "type":"ZONE_VISIT",
  "action":"CREATED",
  "user": {
    "phoneId":"45BC1F13-CA38-4C5B-AD0E-DF3A8299CCB5",
    "customId":"thomas",
    "userAgent":"Mozilla/5.0 (iPhone11,8; CPU iPhone OS 14_4_1 like Mac OS X) FxiOS/3.3.0b529",
    "sdkVersion":"6.3.0",
    "appVersion":"3.3.0",
    "company":"appdemo",
    "sdk":"appdemo_herow3"
  },
  "geofence":{
    "placeId":"1dfksx1xkxqf4",
    "placeExternalId":null,
    "placeName":"Avenue de Mâcon",
    "tags":["common"],
    "location":{
      "lat":46.210631780094744,
      "lng":5.218881486288663,
      "confidence":1.0,
      "geohash":"u05wyu2k"},
    "accuracy":65.0,
    "confidence":1.0,
    "distanceToCenter":101.0,
    "duration":12032,
  },
  "campaign":null,
  "place":{
    "id":"f75dc1e7da8fe3c9582aa3638e2592b5",
    "name":"zone enter test",
    "location": {
      "lat":22.3,
      "lng":3.2,
      "confidence":1.0,
      "geohash":"5d8he"
    },
    "radius":11.0,
    "tags":["my-tag"]
  }
}
```

## Notification Received


### Introduction

Based on the HEROW places, one can create notification campaigns triggered by entry in selected places. 

An event will be generated by the end user's device when he/she received the notification (callback).


### Subscribe to this event 

In the trigger selection in the platform, select "notification received".


### Event Frequency

One event each time a new user receives a notification. If a user receives multiple notification in a row (enters a zone, exit it and re-enter a couple of minutes later), he'll generate multiple events.

### Payload format

```Json
{
  "eventDate":1637006690134,
  "id":"b3e20e53-dea3-42c4-997e-283aa50fc917",
  "type":"NOTIFICATION_RECEIVED",
  "action":"CREATED",
  "user":{
    "phoneId":"phoneId",
    "customId":"customId",
    "userAgent":null,
    "sdkVersion":null,
    "appVersion":null,
    "company":"company",
    "sdk":"sdk"
  },
  "geofence":{
    "placeId":"1dfksx1xkxqf4",
    "placeExternalId":null,
    "placeName":"Avenue de Mâcon",
    "tags":["common"],
    "location":{
      "lat":46.210631780094744,
      "lng":5.218881486288663,
      "confidence":1.0,
      "geohash":"u05wyu2k"},
    "accuracy":65.0,
    "confidence":1.0,
    "distanceToCenter":101.0
  },
  "campaign": {
    "hash":"017d08aa245cb08ffb5ff204e676e4ef",
    "begin":1623070237154,
    "end":2643070273156,
    "company":null,
    "name":"campaign name",
    "tags":["test"],
    "status":"on",
    "capping":{
      "maxNumberNotifications":2.0,
      "minTimeBetweenTwoNotifications":8.64E7
    },
    "daysRecurrence":["MONDAY"],
    "startHour":"10:00",
    "endHour":null,
    "notification":{
      "title":"Hello there",
      "description":"A new week is starting",
      "uri":"https://www.herow.io"
    }
  },
  "place":{
    "id":"f75dc1e7da8fe3c9582aa3638e2592b5",
    "name":"zone test",
    "location": {
      "lat":22.3,
      "lng":3.2,
      "confidence":1.0,
      "geohash":"5d8he"
    },
    "radius":11.0,
    "tags":["my-tag"]
  }
}
```
## Notification Opened


### Introduction

Based on the HEROW places, one can create proximity notification campaigns triggered by entry in selected places. 

An event will be generated by the end user's device when he/she opens the notification (callback). 


### Subscribe to this event 

In the trigger selection in the platform, select "notification opened".


### Event Frequency

One event each time a new user receives a notification. If a user receives multiple notification in a row (enters a zone, exit it and re-enter a couple of minutes later), he'll generate multiple events.

### Payload format
```Json
{
  "eventDate":1619512887033,
  "id":"ef29f70f-b6bb-4738-aa87-8f49192fd143",
  "type":"NOTIFICATION_OPENED",
  "action":"CREATED",
  "user":{
    "phoneId":"45BC1F13-CA38-4C5B-AD0E-DF3A8299CCB5",
    "customId":"super@herow.io",
    "userAgent":"Mozilla/5.0 (iPhone11,8; CPU iPhone OS 14_4_1 like Mac OS X) FxiOS/3.3.0b529",
    "sdkVersion":"6.3.0",
    "appVersion":"3.3.0",
    "company":"appdemo",
    "sdk":"appdemo_herow3"
  },
  "geofence": null,
  "campaign": {
    "hash":"017d08aa245cb08ffb5ff204e676e4ef",
    "begin":1623070237154,
    "end":2643070273156,
    "company":null,
    "name":"campaign name",
    "tags":["test"],
    "status":"on",
    "capping":{
      "maxNumberNotifications":2.0,
      "minTimeBetweenTwoNotifications":8.64E7
    },
    "daysRecurrence":["MONDAY"],
    "startHour":"10:00",
    "endHour":null,
    "notification":{
      "title":"Hello there",
      "description":"A new week is starting",
      "uri":"https://www.herow.io"
    }
  },
  "place":{
    "id":"f75dc1e7da8fe3c9582aa3638e2592b5",
    "name":"zone test",
    "location": {
      "lat":22.3,
      "lng":3.2,
      "confidence":1.0,
      "geohash":"5d8he"
    },
    "radius":11.0,
    "tags":["my-tag"]
  }
}
```