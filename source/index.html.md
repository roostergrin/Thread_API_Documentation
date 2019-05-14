---
title: Thread API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='mailto:info@threadcommunication.com'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Thread API is an interface for developing integrations with Thread products. Currently, Thread Online Scheduling is the only product for which there is a publically available API. This API is organized around [REST](https://en.wikipedia.org/wiki/Representational_state_transfer). 

This document outlines authentication, authorization and resource management for Online Scheduling bookings within the Thread API. API requests, responses and errors are returned as JSON.

# Authentication

Each API consumer is given a ID and a secret. As the name suggests, the secret must remain a secure secret and should never be delivered to a web client. Combine your ID and secret to create a base64 encoded authorization header and request an authorization JSON web token (JWT). This access token is valid for 1 hour.

> To authenticate:

```shell
echo -n 'YOUR_ID:YOUR_SECRET' | openssl base64

curl -X POST \
  https://threadapi.auth.us-west-2.amazoncognito.com/oauth2/token \
  -H 'authorization: Basic ***********iMWl0NmY2MnVlZmVtNmFxaG46ajIyYTJ******HRjYm9yZGtucHR0Z2U0azI5cmE3czgwMjZhZ3J***************' \
  -H 'content-type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials'
```

To register your organization as an API consumer and receive your ID and secret, reach out to <info@threadcommunication.com>.

If you believe your secret key has been made public or has been compromised, please reach out to <info@threadcommunication.com> immediately and we will generate a new key and invalidate your old one.

# Authorization

Every API call must be accompanied by a valid authorization JWT.

`Authorization: access_token`

<aside class="notice">
You must replace <code>access_token</code> with the access token returned from the step above.
</aside>

Different API consumers have different levels of access. Some access levels are scoped to HTTP actions (GET and/or POST) and some are scoped to individual resources or groups of resources. Indicate what level of access your application needs when you reach out for API keys. In some cases, access will first need to be authorized by a business agreement with our integration partners.

<aside class="warning">
You will receive a <code>status: unauthorized</code> response if you are not scoped to the action or resource. If you believe you have reached this message in error, <a href='mailto:info@threadcommunication.com'>please reach out to us.</a>
</aside>

# Thread Online Scheduling API
The Thread Online Scheduling API centers around `booking` resources. A booking is an opening for an initial consultation. When a patient books a booking, they are choosing a real time, e.g. 4:45 PM on Tuesday, and they are expecting an appointment at that exact time. Once a booking is booked, the patient has an appointment.

A booking can have the following states:   

State | Description
----- | -----------
Open| Booking is available
Pending | Temporarily removes the booking from public view while a patient completes the sign up form
Booked | The process is complete and the patient has an appointment

## Get All Thread Online Scheduling Practices

```shell
curl "https://api.threadcommunication.com/scheduling/practices" \
  -H "Authorization: access_token"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": "3rt42",
    "name": "ABC 123 Orthodontics",
    "location_ids": [
      {
        "id": "dr556",
        "name": "Springfield"
      }
    ]
  },
 {
    "id": "rtGB2",
    "name": "Acme Orthodontic Group",
    "location_ids": [
      {
        "id": "b2rGH",
        "name": "Lafayette"
      }
    ]
  },
  {
    "etc": "etc..."
  } 
]
```


This endpoint returns all practices currently using Thread Online Scheduling.

### HTTP Request

`GET https://api.threadcommunication.com/scheduling/practices`

<aside class="success">
  The resources returned by this route are dictated by the level of access required for each integration partner. For instance, practice management systems will be able to retrieve all resources belonging to their customers. Other integration partners my require authorization on a practice-by-practice basis.
</aside>


## Get All Thread Online Scheduling Locations

```shell
curl "https://api.threadcommunication.com/scheduling/locations" \
  -H "Authorization: access_token"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": "b2rGH",
    "name": "Chesterfield",
    "website": "https://www.abc123ortho.com/",
    "street_address": "1234 Main Street",
    "secondary_address": "",
    "city": "New York",
    "state": "New York",
    "zip": "12345",
    "timezone": "America/New_York",
  }
],
```

This endpoint returns all locations using Thread Online Scheduling.

### HTTP Request

`GET https://api.threadcommunication.com/scheduling/locations`

## Get All Bookings for a Practice

```shell
curl "https://api.threadcommunication.com/scheduling/practices/[:id]/bookings" \
  -H "Authorization: access_token"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": "2f4Dr",
    "location_id": "4DFDr",
    "start_datetime": "2018-09-25T16:44:00.000-06:00",
    "length": 60,
    "status": "pending",
    "doctor_id": "DD3456"
  },
  {
    "id": "fdR344",
    "location_id": "4DFDr",
    "start_datetime": "2018-09-22T10:10:00.000-06:00",
    "length": 45,
    "status": "open",
    "doctor_id": "DD3456"
  },
  {
    "etc": "etc..."
  }
]
```

This endpoint gets all bookings for a practice.

<!-- <aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside> -->

### HTTP Request

`GET https://api.threadcommunication.com/scheduling/practices/[:id]/bookings`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the practice to retrieve

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
status | all | Specify booking of a specific status. For multiple statuses, use multiple status queries with `&` between them.

## Get All Bookings for a Location

```shell
curl "https://api.threadcommunication.com/scheduling/locations/[:id]/bookings" \
  -H "Authorization: access_token"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": "2f4Dr",
    "location_id": "4DFDr",
    "start_datetime": "2018-09-25T16:44:00.000-06:00",
    "length": 60,
    "status": "pending",
    "doctor_id": "DD3456"
  },
  {
    "id": "fdR344",
    "location_id": "4DFDr",
    "start_datetime": "2018-09-22T10:10:00.000-06:00",
    "length": 45,
    "status": "open",
    "doctor_id": "DD3456"
  },
  {
    "etc": "etc..."
  }
]
```

This endpoint gets all bookings for an individual location within a practice.

<!-- <aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside> -->

### HTTP Request

`GET https://api.threadcommunication.com/scheduling/locations/[:id]/bookings`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the locations to retrieve

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
status | all | Specify booking of a specific status. For multiple statuses use multiple status queries with `&` between them.

## Get An Individual Booking

```shell
curl "https://api.threadcommunication.com/scheduling/bookings/[:id]" \
  -H "Authorization: access_token"
```

> The above command returns JSON structured like this:

```json
{
    "id": "2f4Dr",
    "location_id": "4DFDr",
    "length": 60,
    "status": "pending",
    "doctor_id": "DD3456", 
}
```

This endpoint gets an individual booking.

### HTTP Request

`GET https://api.threadcommunication.com/scheduling/bookings/[:id]`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the booking to retrieve


## Create a Booking 

```shell
curl "https://api.threadcommunication.com/scheduling/bookings" \
  -H "Authorization: access_token" \
  -H "Content-Type: application/json" \
  -d '{"start": "2018-10-25T16:44:02.722-06:00","status": "pending","recurring": false,"length": 60,"location_id": "4DFDr","doctor_id":"DD3456"}' \
  -X POST 
```

> The above command expects the body to be formatted like this:

```json
{
  "start": "2018-10-25T16:44:02.722-06:00",
  "status": "pending",
  "recurring": false,
  "length": 60,
  "location_id": "4DFDr",
  "doctor_id": "DD3456",
}
```

> And returns JSON structured like this:

```json
{
  "id": "2f4Dr",
  "status": "pending",
  "length": 60,
  "recurring": false,
  "start_datetime": "2018-10-25T16:44:02.722-06:00",
  "location_id": "4DFDr",
  "doctor_id": "DD3456",
}
```

This endpoint creates a booking.

### HTTP Request

`POST https://api.threadcommunication.com/scheduling/bookings`

### Body Attributes

Attribute | Description | Required? | Default
--------- | ----------- | --------- | ----
start | IANA datetime | true | N/A
status | One of `open`, `pending` or `booked`. See [above](#thread-online-scheduling-api) for description. | false | pending
recurring | True if the booking happens weekly | false | false
length | The length of the appointment in minutes | false | 60
location_id | The id of the location where the appointment will take place | true | N/A
doctor_id | The id of the doctor who will see the patient | true | N/A


## Edit a Booking 

```shell
curl "https://api.threadcommunication.com/scheduling/bookings" \
  -H "Authorization: access_token" \
  -H "Content-Type: application/json" \
  -X PUT \
  -d '{"status": "open"}'
```

> The above command expects the body to be formatted like this:

```json
{
  "status": "open"
}
```

> And returns JSON structured like this:

```json
{
  "id": "2f4Dr",
  "status": "open",
  "length": 60,
  "recurring": false,
  "start_datetime": "2018-10-25T16:44:02.722-06:00",
  "location_id": "4DFDr",
  "doctor_id": "DD3456",
}
```

This endpoint edits a booking. You can only edit bookings created using your API keys.

### HTTP Request

`PUT https://api.threadcommunication.com/scheduling/bookings/[:id]`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the booking to edit

Attribute | Description |
--------- | ----------- | 
start | IANA datetime
status | One of `open`, `pending` or `booked`. See [above](#thread-online-scheduling-api) for description.
recurring | True if the booking happens weekly
length | The length of the appointment in minutes
location_id | The id of the location where the appointment will take place
doctor_id | The id of the doctor who will see the patient


## Delete a Booking 

```shell
curl "https://api.threadcommunication.com/scheduling/bookings/[:id]" \
  -H "Authorization: access_token" \
  -X DELETE
```

> It returns JSON structured like this:

```json
{
  "id": "2f4Dr",
  "status": "closed"
}
```

This endpoint deletes a booking. You can only delete bookings created using your API credentials.

### HTTP Request

`DELETE https://api.threadcommunication.com/scheduling/bookings/[:id]`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the booking to delete