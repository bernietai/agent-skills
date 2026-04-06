---
name: bryantpark-courts
description: A court reservation task manager using bryantpark.opensquash.org/home on the browser. Curl commands are setup here to make direct calls to book courts 1,2,3,4 or 5 for a specific user ID
metadata: {
  "openclaw":{
    "requires":{
      "bin":["openclaw browser", "curl"]
    }
  }
}  
---

## User ID

This court reservation task manager is for user ID 810533 only. 

## Court IDs 

There are 5 available courts, identified by the following identifiers: 
- Court 5 = 6710
- Court 4 = 6709
- Court 3 = 6708
- Court 2 = 6707
- Court 1 = 6706

## Browser requests

Only proceed if agent has access to browser, and URL "https://bryantpark.opensquash.org/home" has an active logged-in session. Using the same "Set-Cookie" header, make a POST request using curl to complete the reservation for a specified Court at a specified time. 

## Court reservation rules 

To reserve a court, a POST request to https://bryantpark.opensquash.org/api/courts/{id}/booking_player with the following payload: 

```
{
    "reservation":{
        "date":"2026-04-12",
        "hour_start":{timestamp_start}, 
        "hour_end":{timestamp_end},
        "reservation_type":2,
        "public_game":false,
        "min_ntrp":1,
        "max_ntrp":7,
        "kind":"reservation",
        "ntrp_verified":false
    },
    "payment":{
        "method":"card",
        "payment_intent_id":"",
        "card_details":{
            "lastFourCardDigits":"4668",
            "cardBrand":"Visa",
            "id":830469
        },
        "coupon":{
                    "code":""         },
        "moment":"now"
    },
    "user_ids":[ {user_id} ],
    "user_excluded_ids":[],
    "user_ids_guest_names":{
        "player0":{
            "name":null
        }
    },
    "reservation_fees":[],
    "users_fees":{
        "player0":{
            "fees":[null]
        }
    },
    "auto_fill_courts":true,
    "free_fare_players":[],
    "guest_pass_users":[]
}
```

- timestamp_start and timestamp_end is a representation of the time in minutes from midnight. Examples: 62100 = 17.25 hrs = 5:15pm,  64800 = 18 hrs = 6pm 
- user_id should be fixed to "810533" 
- If user provides a time that is not available as booking slot, use the booking slot when the provided time falls on. eg for provided time 5:30pm, reserve the 5:15pm-6pm slot. 
- In the POST request, include the following headers used in other GET and POST requests in https://bryantpark.opensquash.org/ while logged-in in  browser: 
  - "Cookie" header 
  - "X-Csrf-Token" header 
