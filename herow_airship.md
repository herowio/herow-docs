# Service description

Using HEROW, you can send event directly to Airship to power location triggered notification/messages. 

# Pre-requisites 

## 1. Import Places

Go to your my.Herow.io account under the "Manage" Section.

![image](https://user-images.githubusercontent.com/88100049/144285204-5a563e81-f889-499f-bee0-56d236019709.png)

Select (singular) or Import (multiple zones).


## 2. Turn on the integration

Go to the "Integrate" section under the "Partners" tab and click "Connect with Airship". 

![image](https://user-images.githubusercontent.com/88100049/144285530-4ee45221-ff65-4258-b9b0-cdd716d7b4e1.png)

Past your primary & secondary write keys and select your trigger among the 4 options: 

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

You can now use HEROW events as triggers to send out messages from your Airship account.


# Mapping

You will find below the different fields included in the HEROW to Airship events .

## User Info Mapping

In each of the below events, user

|HEROW User Property      |    Airship User Property  | Type | Exemple
|--------------|-----------|------------|------------|
| "customId" | "named_user_id"     | String        | joedoe@gmail.com|
| "phoneId" | "herow_device_id"     | String        | dc3031ce45abbb3f (Android) / A2A39008-1E97-481B-BF0E-950B0F254321 (iOS) |

## Event Mapping

### Zone Enter

|HEROW Event Property      |    Airship Event Property  | Type | Exemple |
|--------------|-----------|------------|------------------------|
|"zone_enter"  | "zone_enter" or zone    |      |
|"place_id"       | "herow_place_id"  | String    | "1jpk40bpg1ybf"|
|"place_name"       | "herow_place_name"  | String    | "Carrefour Lorient"
|"tags"       | "herow_tags"  | String    | "Carrefour", "supermarket"
| "country"         |" "herow_country" | String  | "FRANCE"    |
|"city"  | "herow_city"  | String    | "Paris 7e Arrondissement"
|"zipCode"  | "herow_zip_code"  | String    | "75010"
|"zone_iris"  | "herow_zone_iris"  | String    | "751072812"
|"geohash"  | herow_geo_hash"| String    | "u0dk5p" 
|"confidence"  | herow_confidence"| Float   | 0.2

### Zone Visit

The _zone visit event_ has similar fields than the _zone enter event_ plus one **duration field (in ms).**

<br>

### Notification sent & received

|HEROW Event Property      |    Airship Event Property  | Type | Exemple |
|--------------|-----------|------------|------------------------|
|"notification_received"  | "   |      |
|      |  |     | |
|      | "herow_place_name"  | String    | "Carrefour Lorient"
|"tags"       | "herow_tags"  | String    | "Carrefour", "supermarket"
| "country"         |" "herow_country" | String  | "FRANCE"    |
|"city"  | "herow_city"  | String    | "Paris 7e Arrondissement"
|"zipCode"  | "herow_zip_code"  | String    | "75010"
|"zone_iris"  | "herow_zone_iris"  | String    | "751072812"
|"geohash"  | herow_geo_hash"| String    | "u0dk5p" 
|"confidence"  | herow_confidence"| Float   | 0.2

