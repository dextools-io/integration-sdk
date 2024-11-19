# Token socials & media upload API

## API access limitations

This API is only available for partners. Please contact marketing@dextools.io to become partner and to request a API key.

## API specs

## POST `/v1/token`

### Description

This endpoint is useful to update social info of your token.

### URL
`/v1/token`

### HTTP Method
`POST`

### Headers
`X-API-Key: YOUR_API_KEY`

### Body fields

| Field                                    | Type   | Required | Description                                                   |
|------------------------------------------|--------|----------|---------------------------------------------------------------|
| `chain`                                  | String | Yes      | Chain.                                                        |
| `address`                                | String | Yes      | Token to update socials.                                      |
| `creationTransactionHash`                | String | No       | Creation transaction hash of token creation. (Only **EVM**)   |
| `socials`                                | Object | No       | Object that contains socials to update.                       |
| `socials.description`                    | String | No       | Description to show in DEXTools.                              |
| `socials.email`                          | String | No       | Email.                                                        |
| `socials.logo`                           | String | No       | Logo in BASE 64 or URL, MAX SIZE: 250x250, MAX WEIGHT: 20KB   |
| `socials.banner`                         | String | No       | BANNER in BASE 64 or URL, MAX SIZE: 600X200. MAX WEIGHT: 200KB|
| `socials.twitter`                        | String | No       | X profile URL.                                                |
| `socials.telegram`                       | String | No       | Telegram channel URL.                                         |
| `socials.discord`                        | String | No       | Discord channel URL.                                          |
| `socials.facebook`                       | String | No       | Facebook URL.                                                 |
| `socials.youtube`                        | String | No       | Youtube channel URL.                                          |
| `socials.instagram`                      | String | No       | Instagram URL.                                                |
| `socials.reddit`                         | String | No       | Reddit URL.                                                   |
| `socials.tiktok`                         | String | No       | Tiktok URL.                                                   |
| `socials.website[]`                      | Array  | No       | Array with website URL.                                       |
| `socials.repos`                          | Object | No       | Object with repositories info                                 |
| `socials.repos.github[]`                  | Array  | No       | Github URLs.                                                 |
| `socials.repos.bitbucket[]`               | Array  | No       | Bitbucket URLs.                                              |

### Body example of a request

```json
{
  "chain": "ether",
  "address": "0x6982508145454ce325ddbe47a25d4ec3d2311933",
  "creationTransactionHash": "0x2afae7763487e60b893cb57803694810e6d3d136186a6de6719921afd7ca304a",
  "socials": {
    "description": "string",
    "email": "string",
    "logo": "string",
    "banner": "string",
    "twitter": "string",
    "telegram": "string",
    "discord": "string",
    "facebook": "string",
    "youtube": "string",
    "instagram": "string",
    "reddit": "string",
    "tiktok": "string",
    "website": [ "string" ],
    "repos": {
      "github": [ "string" ],
      "bitbucket": [ "string" ]
    }
  }
}
```

### Responses

| Code   | Description                               | 
| -------|-------------------------------------------|
| 200    | Returns if token has been created/updated |
| 400 | Bad Request|
|403 | Missing or invalid authorization header|


## GET `/v1/token/:chain/:address`

### Description

This endpoint is useful to check if token has socials info updated or not

### URL
`/v1/token/:chain/:address`

### Parameters
| Field                                    | Type   | Required | Description               |
|------------------------------------------|--------|----------|---------------------------|
| `chain`                                  | String | Yes      | Chain.                    |
| `address`                                | String | Yes      | Token to check socials.   |

### HTTP Method
`GET`

### Example response
```
{
    "statusCode": 200,
    "data": {
        "existingSocialsInfo": true
    }
}
```

### Headers
`X-API-Key: YOUR_API_KEY`

### Responses

| Code | Description                                                                          | 
|------|--------------------------------------------------------------------------------------|
| 200  | Returns existingSocialsInfo: true/false depends on if token has the socials updated  |
| 400  | Bad Request                                                                          |
| 403  | Missing or invalid authorization header                                              |
