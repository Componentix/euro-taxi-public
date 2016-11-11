# Public API Spec

## Headers

Application key *Required for all requests*.
```
X-App-Key: $key
```

Authorization Token *Required for authorized requests*.
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
    name: 'Shukhevich lane'
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
        start: 1000, // drive start price (in minor unit like cents)
        drive: 500, // price per km
        wait: 88, // waiting price per minute
        min: 2000, // min price
        multiplier: 2 // price miltiplier
    }
}]
```

### Tariffs

#### Index

Request:
```
GET /tariffs
```

Response: `200`
*Similar to Options*
```
[{
    id: 2,
    name: 'Comfort',
    price: {
        start: 1000, // drive start price (in minor unit like cents)
        drive: 500, // price per km
        wait: 88, // waiting price per minute
        min: 2000, // min price
        multiplier: 2 // price miltiplier
    }
}]
```

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
    "phone": "+380937661234",
    "confirmed": false,
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9"
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

Response: `201`
```
{
    "phone": "+380937661234",
    "confirmed": true,
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9"
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
- **arrived**: driver arrived
- **running**: ride is started
- **completed**: order is successfuy completed
- **cancelled**: order is cancelled

Response: `200`
```
{
    id: 1,
    status: "searching",
    comment: "waiting for 15 min",
    createdAt: "2015-03-25T12:00:00",
    car: {
        driver: "Alexander",
        info: "Kia Rio (Small 555-888)",
        color: "Black",
        arrivesAt: "2015-03-25T12:00:00"
    },
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
        tariff: {
            id: 2,
            name: "Comfort",
            price: {
                start: 1000,
                drive: 500,
                wait: 88,
                min: 2000,
                multiplier: 2
            }
        },
        options: [{
            id: 3,
            name: "Drive",
            price: {
                start: 1000,
                drive: 500,
                wait: 88,
                min: 2000,
                multiplier: 2
            }
        }, {
            id: 4,
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
        price: 4800
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
    comment: "waiting for 15 min",
    route: {
        calculated: [{
            lat: 123.2,
            lng: 32.1
        }],
        adderesses: [{
            street: "Soborna",
            house: 75,
            comment: "3"
        }]
    },
    modifiers: {
        tariff: {
            id: 3
        },
        options: [{
            id: 1
        }, {
            id: 2
        }],
        tips: 15
    },
    summary: {
        price: 1234
    }
}
```

Response: `201`
```
Same as orders#show
```

#### Destroy

Request:
```
DELETE /orders/:id
```

Response: `200`

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
