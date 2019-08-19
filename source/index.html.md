3---
title: API Reference

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

```javascript
const axios = require('axios');

axios.post('https://api.lightning.gifts/create', { amount: 5000 })
    .then(response => response.data)
    .catch(error => Promise.reject(error));
```

> The above command will return an Invoice:

```json
{
    "order_id": "03a1fb575647d7d79909785c2648e98397ec15e6d6d2562d",
    "charge_id":  "340c81e5-575a-4ff2-9cb6-d6cfe6e953ea",
    "status":  "unpaid",
    "lightning_invoice": {
      "expires_at": 1566215853,
      "payreq": "LNBC5910N1PW44QYAPP5ECXL4F4SW8K67D4WLEDR24E52NKE5Y0SQHJ2R7LYC2CCDCS2XCNQDPVF35KW6R5DE5KUEEQVA5KVAPQVEHHYGP48YCJQUMPW3ESCQZPGRC2RU3M7D204Z6NRUCS26FH90K44M4RY07RL9FKSY85WJWVA9LLHJNCQWQ7Y7MX88F6JRJGEKDTFTU0A5PXK5DRTVXYYWADERZ6TH6CQT5FX0U"
    },
    "amount": 5000,
    "lnurl": "lnurl1dp68gurn8ghj7ctsdyhxc6t8dp6xu6twvuhxw6txw3ej7mrww4exctekxa3rwwt9v3skgv35xf3nzepcvccngvesxpjxyce4xaskydm9x43nsenpxenrjdpjxcmkgcfex9jq8rlhyf"
}
```

This endpoint creates an invoice for a new Lightning Gift. 

After paying the invoice, the gift can be redeemed from the returned order_id at `https://lightning.gifts/redeem/{order_id}` 
or using the returned lnurl.

### HTTPS Request

`POST https://api.lightning.gifts/create`

### Query Parameters

Parameter | Type | Description
--------- | ------- | -----------
`amount` | `Number` | Amount of the gift in Satoshis

# Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

# Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

