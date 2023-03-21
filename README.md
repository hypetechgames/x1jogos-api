# x1jogos-api

# X1 Games - API Documentation

## Introduction
This documentation details the endpoints available on the X1Jogos API.

## Auth
Authentication is done through an API Key that will be sent to the developer. It must be transmitted in all requests in the `Photon-Secret` header. If the API Key is invalid or not informed, X1 Jogos will return `HTTP 401`.

## HTTP Responses codes
X1 Jogos uses conventional HTTP responses to indicate success or failure in requests. Responses with status 200 indicate success, status 4xx indicate failures due to errors in the information sent and status 500 indicate internal errors on the X1 Jogos server.

| **HTTP Code** | **Description** |
|---------------|------------|
| **200** Ok | Your request was successful |
| **400** Bad Request | Some mandatory parameter was not sent or is invalid. In this case the answer itself will indicate what the problem is. |
| **401** Unauthorized | API Key not sent or invalid |
| **429** Too Many Requests | Too many orders in a given period of time. |
| **500** Internal Server Error | Something went wrong on the X1 Games server |

All X1 Games API endpoints receive and respond in JSON.

### Example response for HTTP 400:
```json
{
    "code": 400,
    "message": {
        "error": "Insufficient balance"
    },
    "player": {
        "token": "3947208fj23j203874",
        "user_id": "1",
        "account_id": "2",
        "balance_used": "balance"
    }
}
```

## POST - Create Match
`/webhook/x1/create_match`

### Create a new match
Enables the creation of a new match. In order to be able to create a match, it is first necessary to collect the information of all the users and accounts of the players that will be in the game through a specific cookie so that this information is passed to the endpoints.

All attributes of this endpoint are necessary for the Match to be created, except the `room_bet_value` which, if not sent, will be considered `0` by default.

In the players field, there can be any number of players desired, as long as they all have the required fields.

The HTTP code will be returned, an error or success message, depending on the result of the request, and, if the request has failed, it will also return the data of the player that does not comply with the request, if the request is successful, the match identifier will be returned, which can be used in other API endpoints.

#### Body
```json
{
    "room_bet_value": int,
    "game_code": string,
    "players": [
        {
            "token": string,
            "user_id": string,
            "account_id": string,
            "balance_used": enum ("balance"|"balanceBonus")
        }
    ]
}
```

##### Example
###### Request
```json
{
    "room_bet_value": 50,
    "game_code": "palitinho",
    "players": [
        {
            "token": "472307f23hf3",
            "user_id": "1",
            "account_id": "2",
            "balance_used": "balance"
        },
        {
            "token": "0rjsdof238",
            "user_id": "45",
            "account_id": "90",
            "balance_used": "balanceBonus"
        }
    ]
}
```

###### Response
```json
{
    "code": 400,
    "message": {
        "success": "Match created sucessfully"
    },
    "data": {
        "match_id": 45
    }
}
```


## POST - End Match
`/webhook/x1/end_match`

### Finish a match

When an ongoing match ends, only the match identifier and the account identifier that won that match will be passed to this endpoint. If the match is not in progress or does not exist, an `HTTP 400` code will be returned, if the match is successfully completed, an `HTTP 200` code will be returned with a success message.

#### Body
```json
{
    "match_id": string,
    "winner_id": string
}
```

##### Example
###### Request
```json
{
    "match_id": "4",
    "winner_id": "90"
}
```

###### Response
```json
{
    "code": 200,
    "message": {
        "success": "Match finished successfully"
    }
}
```


## GET - Get player balance
`/webhook/x1/get_player_balance`

### Show player balance
Show all balances of the player who owns the identifier passed in the request. If the player is not found, an `HTTP 400` code is returned.


#### Body
```json
{
    "user_id": string,
    "account_id": string
}
```

##### Example
###### Request
```json
{
    "user_id": "45",
    "account_id": "90"
}
```

###### Response
```json
{
    "code": 200,
    "message": {
        "success": "Account balance retrieved successfully"
    },
    "data": {
        "account_balance": "",
        "account_balanceBonus": ""
    }
}
```


## GET - Get player avatar
`/webhook/x1/get_player_avatar`

### Retrieve the player's avatar

Retrieves the avatar of a specific account, this avatar will be base64 encoded. If the account is not found, an `HTTP 400` code will be returned. If the account does not have an avatar, an `HTTP 200` code will be returned, but with the `account_avatar_base64 = null` field.


#### Body
```json
{
    "user_id": string,
    "account_id": string
}
```

##### Example
###### Request
```json
{
    "user_id": "45",
    "account_id": "90"
}
```

###### Response
```json
{
    "code": 200,
    "message": {
        "success": "Player avatar retrieved successfully"
    },
    "data": {
        "account_avatar_base64": ""
    }
}
```


## GET - Get account pending match
`/webhook/x1/get_account_pending_match`

### Retrieves the match the account is in
It retrieves the `match_id` of a `pending` match that the last account is in, if the account is not found, an `HTTP 400` code will be returned. If the account is not in any `pending` match, an `HTTP 200` code will be returned, but the field `match_id = null`.

#### Body
```json
{
    "user_id": string,
    "account_id": string
}
```

##### Example
###### Request
```json
{
    "user_id": "45",
    "account_id": "90"
}
```

###### Response
```json
{
    "code": 200,
    "message": {
        "success": "Pending match retrieved successfully"
    },
    "data": {
        "match_id": "5"
    }
}
```
