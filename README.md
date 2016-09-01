# Public API Spec

## Start

```
node apps/public-api/app
```

## Usage

## Headers

Application key *Required for all requests*.
```
X-App-Key: $key
```

Authorization JWT token *Required for authorized requests*.
```
Authorization: Bearer $token
```

*Required for all requests with JSON body*.
```
Content-Type: application/json
```

## Routes

### Places

#### Index

Request:
```
GET /places?q=:query
```

Response: `200`
```
[{
    name: 'Bandera street'
}, {
    name: 'Bandera lane'
}]
```

### Options

#### Index

Request:
```
GET /options
```

Response: `200`
```
[{
    id: 1,
    name: 'Animals',
    price: {
        start: 1000,
        drive: 500,
        wait: 88,
        min: 2000,
        multiplier: 2
    }
}]
```

- start: drive start price (in minor unit like cents)
- drive: price per km
- wait: price per minute
- min: min price
- multiplier: price miltiplier

### Tariffs

#### Index

Request:
```
GET /tariffs
```

Response: `200`
```
[{
    id: 2,
    name: 'Comfort',
    price: {
        start: 1000,
        drive: 500,
        wait: 88,
        min: 2000,
        multiplier: 2
    }
}]
```

Same price fileds as options

### Session

#### Create

Request:
```
POST /session
```

```
{
    "phone": "+380937660123"
}
```

Response: `201`
```
{
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJwaG9uZSI6IiszODA5Mzc2NjAxMjMifQ.KYOW0H08XBDOUmIcXfI2JskxAYEKPG0iHYI_Cdj06MU"
}
```

### Session Confirmation

**Requires Unconfirmed Authorization**

Request:
```
POST /session/confirmation
```

```
{
    "confirmation": "1234"
}
```

### Orders

**Requires Authorization**

#### Index

Request:
```
GET /orders
```

Response: `200`
```
Same as orders#show
```

#### Show

Request:
```
GET /orders/:id
```

*All Time/Dates in UTC*

Status one of:
- **searching**: searching for a car
- **accepted**: accepted by driver
- **ready**: driver arrived
- **running**: ride is started
- **completed**: order is successfuy completed
- **cancelled**: order is cancelled

Response: `200`
```
{
    id: 1,
    status: "searching",
    cancelReason: "police",
    time: "2015-03-25T12:00:00",
    comment: "waiting for 15 min",
    route: {
        addresses: [{
            street: "Soborna",
            house: "75",
            place: "3",
            location: {
                lat: 49.48163,
                lng: 23.76362
            }
        }],
        calculated: [{
            lat: 49.48163,
            lng: 23.76362
        }],
        actual: [{
            lat: 49.48163,
            lng: 23.76362
        }],
        currentLocation: {
            lat: 49.48123,
            lng: 23.76342
        }
    },
    modifiers: {
        options: [{
            name: "Drive"
        }, {
            name: "Animals"
        }],
        tips: 1500
    },
    feeback: {
        rating: 3.5
    },
    summary: {
        distance: 22000,
        waiting: 48000,
        total: 4800
    }
}
```

#### Create

Request:
```
POST /orders
```

```
{
    time: "2015-03-25T12:00:00",
    comment: "waiting for 15 min",
    route: {
        adderesses: [{
            street: "Soborna",
            house: 75,
            comment: "3"
        }]
    }
    modifiers: {
        options: [{
            id: 1
        }, {
            id: 2
        }],
        tips: 15
    }
}
```

Response: `201`
```
Same as orders#show
```

### Order Feedback

**Requires Authorization**

#### Create

Request:
```
POST /orders/:id/feedback
```

```
{
    "rating": 3.5
}
```

### Errors

```
{
    "error": "Unprocessible entity",
    "data": {
        "base": ["error1", "error2"],
        "field1": ["error11", "error12"]
    }
}
```
