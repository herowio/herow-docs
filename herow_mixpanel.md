# Service description

Using HEROW, you can send event directly to Mixpanel to be analyze your users' trafic at your HEROW places & measure the direct impact of proximity-based notification 

# Pre-requisites 

## 1. Import Places

Go to your my.Herow.io account under the "Manage" Section.

![image](https://user-images.githubusercontent.com/88100049/144285204-5a563e81-f889-499f-bee0-56d236019709.png)

Select (singular) or Import (multiple zones).


## 2. Turn on the integration

Go to the "Integrate" section under the "Partners" tab and click "Connect with Mixpanel". 

![image](https://user-images.githubusercontent.com/88100049/144296626-c2807a00-8d4b-4313-808a-375769f2dc34.png)


Past your mixpanel tokenm & secret API keys and select your trigger among the 4 options: 

- Zone Enter: _each time a user enters a place._

- Zone Visit: _each time a user exits a place._
- Notification sent: _each time a user receives a notification._
- Notification opened: _each time a user opens a notification._

<br>

>Note: 
><br>
>If the user turns off his device while in the place (with foreground location permission only), the **zone visit** event will be sent the next time your app gets location access (next session), from a few hours to many days later.
> 

## 3. That's it! :) 

You can now use HEROW events build custom reports in your Mixpanel account. 
<br>
<br>
# Event payloads

Find below the payloads including all data fields to be used to personnalize your messages' content or trigger messages based on specific locations or brands. 

Create specific foot trafic reports based on visits at all your Carrefours and compare this with all supermarkets. 

Information contained in events can also be used to create user segments to address. 

## Zone Enter & Visit
<br>

```Json
  "event": "ZONE_ENTER",

  "properties": {
    "$mp_api_timestamp_ms": 1636555130743,
    "city": "Bourg-en-Bresse",
    "confidence": "CustomEventPropertyValue{value=0.63, propertyType=NUMBER}",
    "country": "FRANCE",
    "custom_id": "joe.doe@gmail.com",
    "device_id": "734B2EA7-AFCE-4CEF-95B5-5DD48A17E159",
    "event_name": "ZONE_ENTER",  // or "ZONE_VISIT"
    "geo_hash": "u05wes",
    "place_id": "030556dd728131c8c1c535c4e7df9f71",
    "place_name": "Carrefour Rennes",
    "tag": [
      "Carrefour",
      "Rennes",// up to 4 tags per place
      "Supermarket",
    ],
    "timestamp": "2021-11-10T15:38:50",
    "zip_code": "01053",
    "zone_iris": "010530201",
    "duration": 12000, // Only for the zone visit event
```

## Notification received & opened
<br>

```Json
{
  "event": "NOTIFICATION_RECEIVED", // or NOTIFICATION_OPENED
  "properties": {
    "time": 1637790049, // Unix format
    "distinct_id": "customId",
    "campaign_hash": "60aa72d692a46807d7311d10", // Unique campaign id
    "campaign_name": "test notification",
    "campaign_tags": [
      "60aa725e735bff0d5456a0a9"
    ], 
    "city": "Paris 7e Arrondissement(IT)", // Geographical information
    "country": "FRANCE",
    "geo_hash": "u0dk5p",
    "zip_code": "75107",
    "zone_iris": "751072812",

    "custom_id": "customId", // Generally user's email
    "device_id": "phoneId",  // Unique device id
   
    "place_id": "placeId", // Place information
    "place_name": "placeName",
    "place_tags": [
      "tag1",
      "tag2"
    ],
    "timestamp": "2021-11-24T22:40:49", // Event timestamp
  }
}
```
# Validate your integration

## Events on Mixpanel 

In your Mixpanel account, check diectly in the event tab. You should see all events appearing chronologically. 

![image](https://user-images.githubusercontent.com/88100049/144440063-c27fa0e6-3abd-4777-85ad-277246599473.png)

>Note: 
<br>
> Even though events are live-streamed from end-users' devices, in some cases devices are communicating with some latency (no network at the time of the event) which might add some occurences later. 
>
>_Ex: I enter a shop at 7:00 pm but have no network before 7:30._
'
## Build Custom Reports

Using HEROW's event, you can now analyse your trafic on multiple layers (from geography to specific brands or even to the shop level).

![image](https://user-images.githubusercontent.com/88100049/144443144-994d75fe-4251-42b1-9bd9-3a9b6f14082b.png)
(here a report breaking down foot trafic by city).
