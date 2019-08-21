---
title: Lightning Gifts API

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: false
---

# Introduction

Welcome to the Kittn API! You can use our API to access Kittn API endpoints, which can get information on various cats, kittens, and breeds in our database.

We have language bindings in Shell, Ruby, Python, and JavaScript! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This example API documentation page was created with [Slate](https://github.com/lord/slate). Feel free to edit it and use it as a base for your own API's documentation.

<!---
# Authentication

> To authorize, use this code:

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>
-->

# Create Gift

## /create

```javascript
const axios = require('axios');

axios.post('https://api.lightning.gifts/create', { amount: 5000, notify: 'https://your webhook url' })
    .then(response => response.data)
    .catch(error => Promise.reject(error));
```

> 200 OK:

```json
{
    "order_id": "03a1fb575647d7d79909785c2648e98397ec15e6d6d2562d",
    "charge_id":  "340c81e5-575a-4ff2-9cb6-d6cfe6e953ea",
    "status":  "unpaid",
    "lightning_invoice": {
      "expires_at": 1566215853,
      "payreq": "LNBC..."
    },
    "amount": 5000,
    "lnurl": "lnurl..."
}
```

Creates a BOLT-11 invoice for a new Lightning Gift. 

After paying the invoice, the Lightning Gift will be created. The gift can then be redeemed at `https://lightning.gifts/redeem/{order_id}` 
or using the `lnurl`.

### HTTPS Request

`POST https://api.lightning.gifts/create`

### Query Parameters

Parameter | Type | Description
--------- | ------- | -----------
`amount` | number | *Required* Amount of the gift in Satoshis
`notify` | string | *Optional* URL to receive webhook when gift is redeemed. Must start with `http` or `https`

# Check Gift Invoice Status

## /invoice_status

```javascript
const axios = require('axios');

return axios.get(`https://api.lightning.gifts/status/${charge_id}`)
    .then(response => response.data)
    .catch(error => Promise.reject(error));
```

> 200 OK:

```json
{
    "status":  "unpaid/processing/paid"
}
```

Returns the status of an invoice generated from `/create`.

### HTTP Request

`GET https://api.lightning.gifts/status/<charge_id>`

### URL Parameters

Parameter | Type | Description
--------- | ------- | -----------
`charge_id` | string | *Required* Charge ID of invoice created in `/create`

# Redeem Gift

## /redeem

```javascript
const axios = require('axios');

return axios.post(`https://api.lightning.gifts/redeem/${order_id}`, { invoice: 'LNBC...' })
    .then(response => response.data)
    .catch(error => Promise.reject(error));
```

> 200 OK:

```json
{
  "withdrawal_id": "ba8f8f99-8b03-4b22-9275-4bf2b8e3e7c2"
}
```

Redeem a Lightning Gift. 

Invoice must be for **exactly** the same amount of the gift.

Returns a `withdrawal_id` that you can use to check the status of the gift redeem using `/redeemStatus`. 

### HTTP Request

`POST https://api.lightning.gifts/redeem/<order_id>`

### URL Parameters

Parameter | Type | Description
--------- | ------- | -----------
`order_id` | string | *Required* Order ID from `/create`

### Query Parameters

Parameter | Type | Description
--------- | ------- | -----------
`invoice` | string | *Required* BOLT-11 invoice to receive the gift

# Check Gift Redeem Status

## /redeem_status

```javascript
const axios = require('axios');

return axios.get(`https://api.lightning.gifts/redeemStatus/${withdrawal_id}`)
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

`GET https://api.lightning.gifts/redeemStatus/<withdrawal_id>`

### URL Parameters

Parameter | Type | Description
--------- | ------- | -----------
`withdrawal_id` | string | *Required* Withdrawal ID provided by `/redeem`



