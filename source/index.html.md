---
title: Lightning Gifts API

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript

search: false
---

# Lightning Gifts API

Welcome to Lightning Gifts API docs! If you have any issues, need help with using the API or lnurl DM @baebb_code on Twitter.

Before you begin using the API [please fill out this form](https://docs.google.com/forms/d/e/1FAIpQLSdzOftJb1n2OJv5s7QhH21kbLgSKiQ0YIjm1uaBnKFd197V2w/viewform?usp=send_form) so I can understand your needs better and email you with API changes.

# Rate limits

Requests are rate limited to prevent abuse and encourage best practises. Please use webhooks instead of querying `/gift` to get create or redeem confirmations. 

# Create Gift

## `POST` /create

```javascript
const axios = require('axios');

axios.post('https://api.lightning.gifts/create', { 
    amount: 5000, 
    notify: 'https://your webhook url', // optional
    senderName: 'Name of sender', // optional
    senderMessage: 'Message to the recipient' // optional
  })
    .then(response => response.data)
    .catch(error => Promise.reject(error));
```

> 200 OK:

```json
{
  "orderId": "03a1fb575647d7d79909785c2648e98397ec15e6d6d2562d",
  "chargeId":  "340c81e5-575a-4ff2-9cb6-d6cfe6e953ea",
  "status":  "unpaid",
  "lightningInvoice": {
    "expires_at": 1566215853,
    "payreq": "LNBC..."
  },
  "amount": 5000,
  "lnurl": "lnurl...",
  "senderName":  null,
  "senderMessage":  "Hello world"
}
```

Creates a BOLT-11 invoice for a new Lightning Gift. 

After paying the invoice, the Lightning Gift will be created. The gift can then be redeemed at `https://lightning.gifts/redeem/{orderId}` 
or using the `lnurl`.

### HTTPS Request

`POST https://api.lightning.gifts/create`

### Body Parameters

Parameter | Type | Description
--------- | ------- | -----------
`amount` | number | *Required* Amount of the gift in Satoshis
`notify` | string | *Optional* URL to receive webhook when gift is redeemed. Must start with `http` or `https`
`senderName` | string | *Optional* Name of sender
`senderMessage` | string | *Optional* Message to the recipient
`verifyCode` | number | *Optional* Add a 4-digit security code to your gift

### Rate limit

50/15 minutes

<!-- 
# Check Gift Invoice Status [Depreciated]

## /invoiceStatus

```javascript
const axios = require('axios');

return axios.get(`https://api.lightning.gifts/status/${chargeId}`)
  .then(response => response.data)
  .catch(error => Promise.reject(error));
```

> 200 OK:

```json
{
  "status": "unpaid/processing/paid"
}
```

Returns the status of an invoice generated from `/create`.

### HTTP Request

`GET https://api.lightning.gifts/status/<chargeId>`

### URL Parameters

Parameter | Type | Description
--------- | ------- | -----------
`chargeId` | string | *Required* Charge ID of invoice created in `/create`
-->

# Get Gift Details

## `GET` /gift

```javascript
const axios = require('axios');

return axios.get(`https://api.lightning.gifts/gift/${orderId}`)
  .then(response => response.data)
  .catch(error => Promise.reject(error));
```

> 200 OK unspent:

```json
{
  "amount": 1000,
  "id": "a8bd69283e8669b6c3c9a47f98bb844792d828f66293d4g6",
  "notify": null,
  "createdAt": {
      "_seconds": 1566648057,
      "_nanoseconds": 128000000
  },
  "chargeStatus": "paid",
  "chargeInfo": {
      "chargeId": "8b0b8194-f602-40ab-8f42-ba6387ac93d7",
      "chargeInvoice": "LNBC..."
  },
  "spent": false,
  "orderId": "a8bd69283e8669b6c3c9a47f98bb844792d828f66293d4g6",
  "lnurl": "lnurl...",
  "senderName":  null,
  "senderMessage":  "Hello world"
}
```

> 200 OK spent:

```json
{
  "amount": 1000,
  "id": "a8bd69283e8669b6c3c9a47f98bb844792d828f66293d4g6",
  "notify": null,
  "createdAt": {
      "_seconds": 1566648057,
      "_nanoseconds": 128000000
  },
  "chargeStatus": "paid",
  "chargeInfo": {
      "chargeId": "8b0b8194-f602-40ab-8f42-ba6387ac93d7",
      "chargeInvoice": "LNBC..."
  },
  "spent": true,
  "orderId": "a8bd69283e8669b6c3c9a47f98bb844792d828f66293d4g6",
  "lnurl": "lnurl...",
  "senderName":  null,
  "senderMessage":  "Hello world",
  "withdrawalInfo": {
      "withdrawalId": "ge35637a-a80e-4c54-81c8-7971e87f4df2",
      "withdrawalInvoice": "lnbc...",
      "createdAt": {
          "_seconds": 1566758278,
          "_nanoseconds": 18000000
      },
      "fee": "0"
  }	
}
```

Gets a gift's details.

### HTTP Request

`GET https://api.lightning.gifts/gift/<orderId>`

### URL Parameters

Parameter | Type | Description
--------- | ------- | -----------
`orderId` | string | *Required* Order ID provided by `/create`

### Query Parameters

Parameter | Type | Description
--------- | ------- | -----------
`verifyCode` | number | *Optional* For gifts with `verifyCode` set in `/create` 

### Rate limit

200/15 minutes

# Redeem Gift

## `POST` /redeem

```javascript
const axios = require('axios');

return axios.post(`https://api.lightning.gifts/redeem/${orderId}`, { 
    invoice: 'LNBC...' 
  })
    .then(response => response.data)
    .catch(error => Promise.reject(error));
```

> 200 OK:

```json
{
  "withdrawalId": "ba8f8f99-8b03-4b22-9275-4bf2b8e3e7c2"
}
```

Redeem a Lightning Gift. 

Invoice must be for **exactly** the same amount of the gift.

Returns a `withdrawalId` that you can use to check the status of the gift redeem using `/redeemStatus`. 

### HTTP Request

`POST https://api.lightning.gifts/redeem/<orderId>`

### URL Parameters

Parameter | Type | Description
--------- | ------- | -----------
`orderId` | string | *Required* Order ID from `/create`

### Body Parameters

Parameter | Type | Description
--------- | ------- | -----------
`invoice` | string | *Required* BOLT-11 invoice to receive the gift
`verifyCode` | number | *Optional* For gifts with `verifyCode` set in `/create`

### Rate limit

50/15 minutes

# Check Gift Redeem Status

## `GET` /redeemStatus

```javascript
const axios = require('axios');

return axios.get(`https://api.lightning.gifts/redeemStatus/${withdrawalId}`)
  .then(response => response.data)
  .catch(error => Promise.reject(error));
```

> 200 OK:

```json
{
  "reference": "LNBC...",
  "status": "initial/confirmed"
}
```

Returns the status of a gift redeem using `/redeem`. `reference` is the BOLT-11 invoice that was used.

### HTTP Request

`GET https://api.lightning.gifts/redeemStatus/<withdrawalId>`

### URL Parameters

Parameter | Type | Description
--------- | ------- | -----------
`withdrawalId` | string | *Required* Withdrawal ID provided by `/redeem`

### Rate limit

200/15 minutes



