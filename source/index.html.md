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

Thread API is an interface for developing integrations with Thread products. Currently, OpenChair Scheduling is the only product for which there is a publically available API. This API is organized around [REST](https://en.wikipedia.org/wiki/Representational_state_transfer). 

This document outlines authentication, authorization and resource management for OpenChair bookings within the Thread API. API requests, responses and errors are returned as JSON.

# Authentication

Thread API uses API keys to allow access. This API key must remain a secret. Web clients are prohibited from using this API directly. Thread API expects the API key to be included in all API requests.

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: superlongcharacterstring"
```

> Make sure to replace `superlongcharacterstring` with your API key.

To obtain an API key, reach out to <info@threadcommunication.com>

If you believe your key has been made public or has been compromised, please reach out to <info@threadcommunication.com> immediately and we will generate a new key and invalidate your old key.

# Authorization

Many resources are available without special authorization. Resources marked with a 🔒 require additional authorization. All POST, PUT and DELETE requests require special authorization.

`Authorization: superlongcharacterstring`

<aside class="notice">
You must replace <code>superlongcharacterstring</code> with your personal API key.
</aside>

# OpenChair Scheduling API
The OpenChair Scheduling API centers around `booking` resources. A booking is an opening for an initial consultation.

There are two main categories of bookings, bookings and range bookings.   

  1. **Booking**: a booking is an opening in the schedule for an initial consultation. When a patient books a booking, they are choosing a real time, e.g. 4:45 PM on Tuesday, and they are expecting an appointment at that exact time. Once a booking is booked, the patient has an appointment.
  2. **Range Booking**: when a patient books a range booking, they are requesting a consultation within a time range (morning, late morning, early afternoon or late afternoon) and they are expecting the office to get back to them with the exact time of their appointment.

A booking can have the following states:   

State | Description
----- | -----------
Open| Booking is available
Pending | Temporarily removes the booking from public view while a patient completes the sign up form
Reserved | Patient has booked but needs an exact time. Only applies to range bookings
Booked | The process is complete and the patient has an appointment

<aside class="notice">
Practices will have <code>bookings</code> or <code>range bookings</code> but not a mix of both.
</aside>

## Get All OpenChair Practices 🔒

```shell
curl "https://api.threadcommunication.com/scheduling/practices"
  -H "Authorization: superlongcharacterstring"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": "3rt42",
    "name": "ABC 123 Orthodontics",
    "location_ids": ["dr556", "rfW2X"]
  },
 {
    "id": "rtGB2",
    "name": "Acme Orthodontic Group",
    "location_ids": ["b2rGH", "HK295"]
  },
  {
    "etc": "etc..."
  } 
]
```


This endpoint returns all practices currently using OpenChair Scheduling.

### HTTP Request

`GET https://api.threadcommunication.com/scheduling/practices`

<!-- <aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside> -->


## Get an Individual Location

```shell
curl "https://api.threadcommunication.com/scheduling/location/[:id]"
  -H "Authorization: superlongcharacterstring"
```

> The above command returns JSON structured like this:

```json
{
  "id": "b2rGH",
  "practice_id": "rtGB2",
  "name": "Chesterfield",
  "office_phone": "555 555 5555",
  "street_address": "1234 Main Street",
  "city": "Ney York",
  "state": "New York",
  "zip": "12345",
  "timezone": "America/New_York",
  "latitude": 26.6545731,
  "longitude": -80.2043307,
}
```

This endpoint returns a location given a location id.

### HTTP Request

`GET https://api.threadcommunication.com/scheduling/location/[:id]`

## Get All Bookings for a Practice

```shell
curl "https://api.threadcommunication.com/onlinescheduling/practice/[:id]/bookings"
  -H "Authorization: superlongcharacterstring"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": "2f4Dr",
    "status": "pending",
    "length": 60,
    "recurring": false,
    "start_datetime": "2018-09-25T16:44:02.722-06:00",
    "location_id": "4DFDr",
    "doctor_id": "DD3456",
    "available_appointments": 2,
    "total_appointments": 3,
    "updated_at": "2018-10-25T16:44:02.722-06:00",
    "created_at": "2017-02-23T16:44:02.722-06:00",
    "range_booking": false
  },
  {
    "id": "2f4Dr",
    "status": "pending",
    "length": 60,
    "recurring": false,
    "start_datetime": "2018-09-25T16:44:02.722-06:00",
    "location_id": "4DFDr",
    "doctor_id": "DD3456",
    "available_appointments": 2,
    "total_appointments": 3,
    "range_booking": false,
    "updated_at": "2018-10-25T16:44:02.722-06:00",
    "created_at": "2017-02-23T16:44:02.722-06:00",
  },
  {
    "etc": "etc..."
  }
]
```

This endpoint gets all bookings for a practice.

<!-- <aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside> -->

### HTTP Request

`GET https://api.threadcommunication.com/onlinescheduling/practice/[:id]/bookings`

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
curl "https://api.threadcommunication.com/onlinescheduling/locaiton/[:id]/bookings"
  -H "Authorization: superlongcharacterstring"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": "2f4Dr",
    "status": "pending",
    "length": 60,
    "recurring": false,
    "location_id": "4DFDr",
    "doctor_id": "DD3456", 
  },
  {
    "id": "2f4Dr",
    "status": "pending",
    "length": 60,
    "recurring": false,
    "location_id": "4DFDr",
    "doctor_id": "DD3456", 
  },
  {
    etc...
  }
]
```

This endpoint gets all bookings for an individual location within a practice.

<!-- <aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside> -->

### HTTP Request

`GET https://api.threadcommunication.com/onlinescheduling/location/[:id]/bookings`

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
curl "https://api.threadcommunication.com/onlinescheduling/booking/[:id]"
  -H "Authorization: superlongcharacterstring"
```

> The above command returns JSON structured like this:

```json
{
    "id": "2f4Dr",
    "status": "pending",
    "length": 60,
    "recurring": false,
    "location_id": "4DFDr",
    "doctor_id": "DD3456", 
  }
```

This endpoint gets an individual booking.

### HTTP Request

`GET https://api.threadcommunication.com/onlinescheduling/booking/[:id]`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the booking to retrieve


## Create a Booking 🔒

```shell
curl "https://api.threadcommunication.com/onlinescheduling/booking"
  -H "Authorization: superlongcharacterstring"
  -H "Content-Type: application/json"
  -d '{"id": "2f4Dr", "status": "pending", "length": 60, "recurring": false, "location_id": "4DFDr", "doctor_id": "DD3456",}'
  -X POST 
```

> The above command expects the body to be formatted like this:

```json
{
  "id": "2f4Dr",
  "status": "pending",
  "length": 60,
  "recurring": false,
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
  "location_id": "4DFDr",
  "doctor_id": "DD3456",
}
```

This endpoint creates a booking.

### HTTP Request

`POST https://api.threadcommunication.com/onlinescheduling/booking`


## Edit a Booking 🔒

```shell
curl "https://api.threadcommunication.com/onlinescheduling/booking"
  -H "Authorization: superlongcharacterstring"
  -H "Content-Type: application/json"
  -X PUT
  -d '{"id": "2f4Dr", "status": "pending", "length": 60, "recurring": false, "location_id": "4DFDr", "doctor_id": "DD3456",}'
```

> The above command expects the body to be formatted like this:

```json
{
  "status": "reserved",
}
```

> And returns JSON structured like this:

```json
{
  "id": "2f4Dr",
  "status": "reserved",
  "length": 60,
  "recurring": false,
  "location_id": "4DFDr",
  "doctor_id": "DD3456", 
}
```

This endpoint edits a booking. You can only edit bookings created using your API key.

### HTTP Request

`POST https://api.threadcommunication.com/onlinescheduling/booking/[:id]`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the booking to edit

## Delete a Booking 🔒

```shell
curl "https://api.threadcommunication.com/onlinescheduling/booking/[:id]"
  -H "Authorization: superlongcharacterstring"
  -X DELETE
```

> It returns JSON structured like this:

```json
{
  "id": "2f4Dr",
  "status": "closed"
}
```

This endpoint deletes a booking. You can only delete bookings created using your API key.

### HTTP Request

`DELETE https://api.threadcommunication.com/onlinescheduling/booking/[:id]`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the booking to delete