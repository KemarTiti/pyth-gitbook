# pyth-client Websocket API

The pythd daemon supports a websocket interface based on the json-rpc 2.0 standard. Methods include:

* [get\_product\_list](pyth-client-websocket-api.md#get\_product\_list)
* [update\_price](pyth-client-websocket-api.md#update\_price)
* [subscribe\_price\_sched](pyth-client-websocket-api.md#subscribe\_price\_sched)
* [get\_product](pyth-client-websocket-api.md#get\_product)
* [get\_all\_products](pyth-client-websocket-api.md#get\_all\_products)

Batch requests are processed in the order the requests appear within the batch.

## get\_product\_list

Get the list of available symbols and associated metadata.

The request looks like:

```
{
  "jsonrpc": "2.0",
  "method": "get_product_list",
  "id" : 1
}
```

A successful response looks something like:

```
{
 "jsonrpc": "2.0",
 "result": [
  {
   "account": "9F6eBgAfktth93C9zmtKDXFXNjZkq6JwJR56VPKqWmJm",
   "attr_dict": {
    "symbol": "SYMBOL1/USD",
    "asset_type": "Equity",
    "country": "USA",
    "description": "pyth example product #1",
    "quote_currency": "USD",
    "tenor": "Spot",
    "cms_symbol": "SYMBOL1",
    "cqs_symbol": "SYMBOL1",
    "nasdaq_symbol": "SYMBOL1"
   },
   "price": [
    {
     "account": "CrZCEEt3awgkGLnVbsv45Pp4aLhr7fZfZr3ubzrbNXaq",
     "price_exponent": -4,
     "price_type": "price"
    }
   ]
  },
  {
   "account": "HCFaDYyz1ajS57HfCaaqzA1cZSaa2oEccQejbHaaofd4",
   "attr_dict": {
    "symbol": "SYMBOL2/USD",
    "asset_type": "Equity",
    "country": "USA",
    "description": "pyth example product #2",
    "quote_currency": "USD",
    "tenor": "Spot",
    "cms_symbol": "SYMBOL2",
    "cqs_symbol": "SYMBOL2",
    "nasdaq_symbol": "SYMBOL2"
   },
   "price": [
    {
     "account": "7FUsKvvtN5rB1fgYFWZLo5DLcqHTTeu63bUPThYT6MiS",
     "price_exponent": -4,
     "price_type": "price"
    }
   ]
  }
 ],
 "id": null
}
```

## update\_price

Update component price of some symbols using the publishing key of the pythd daemon.

The request includes the pricing account from the get\_product\_list output and looks something like:

```
{
  "jsonrpc": "2.0",
  "method": "update_price",
  "params" : {
    "account": "CrZCEEt3awgkGLnVbsv45Pp4aLhr7fZfZr3ubzrbNXaq",
    "price" : 42002,
    "conf" : 3,
    "status": "trading"
  },
  "id" : 1
}
```

The price and confidence interval (conf) attributes are expressed as integers with an implied decimal point given by the price_exponent defined by the symbol. The price type is a string with one of the following values: "price" or "ema_\_price". The symbol status is a string with one of the following values: "trading" or "halted".

A successful response looks like:

```
{
  "jsonrpc": "2.0",
  "result" : 0,
  "id" : 1
}
```

## subscribe\_price\_sched

Subscribe to price update schedule. pythd will notify the client whenever it should submit the next price for a subscribed symbol.

The request looks like:

```
{
  "jsonrpc": "2.0",
  "method": "subscribe_price_sched",
  "params" : {
    "account": "CrZCEEt3awgkGLnVbsv45Pp4aLhr7fZfZr3ubzrbNXaq",
  },
  "id" : 1
}
```

A successful response looks like:

```
{
  "jsonrpc": "2.0",
  "result" : {
    "subscription" : 1234
  },
  "id" : 1
}
```

Where the result is an integer corresponding to a subscription identifier. All subsequent notifications for this subscription correspond to this identifier.

```
{
  "jsonrpc": "2.0",
  "method": "notify_price_sched",
  "params": {
    "subscription" : 1234
  }
}
```

### get\_product

Get the full set of data for the given product

The request looks like:

```
{
  "jsonrpc": "2.0",
  "method": "get_product",
  "params": {
    "account": "4aDoSXJ5o3AuvL7QFeR6h44jALQfTmUUCTVGDD6aoJTM"
  },
  "id" : 1
}
```

A successful response looks something like:

```
{
  "jsonrpc": "2.0",
  "result": {
    "account": "4aDoSXJ5o3AuvL7QFeR6h44jALQfTmUUCTVGDD6aoJTM",
    "attr_dict": {
      "asset_type": "Crypto",
      "symbol": "BTC/USD",
      "country": "US",
      "quote_currency": "USD",
      "description": "BTC/USD",
      "tenor": "Spot",
      "generic_symbol": "BTCUSD"
    },
    "price_accounts": [
      {
        "account": "GVXRSBjFk6e6J3NbVPXohDJetcTjaeeuykUpbQF8UoMU",
        "price_type": "price",
        "price_exponent": -8,
        "status": "trading",
        "price": 4426101900000,
        "conf": 4271150000,
        "ema_price": 4433467600000,
        "ema_confidence": 1304202670,
        "valid_slot": 91402257,
        "pub_slot": 91402259,
        "prev_slot": 91402256,
        "prev_price": 4425895500000,
        "prev_conf": 3315350000,
        "publisher_accounts": [
          {
            "account": "HekM1hBawXQu6wK6Ah1yw1YXXeMUDD2bfCHEzo25vnEB",
            "status": "trading",
            "price": 4426958500000,
            "conf": 1492500000,
            "slot": 91402255
          },
          {
            "account": "GKNcUmNacSJo4S2Kq3DuYRYRGw3sNUfJ4tyqd198t6vQ",
            "status": "trading",
            "price": 4424690000000,
            "conf": 3690000000,
            "slot": 91402256
          }
        ]
      }
    ]
  },
  "id": 1
}
```

### get\_all\_products

Get full set of data for the given product

The request looks like:

```
{
  "jsonrpc": "2.0",
  "method": "get_all_products",
  "id" : 1
}
```

A successful response looks something like:

```
{
  "jsonrpc": "2.0",
  "result": [
    {
      "account": "5uKdRzB3FzdmwyCHrqSGq4u2URja617jqtKkM71BVrkw",
      "attr_dict": {
        "asset_type": "Crypto",
        "symbol": "BCH/USD",
        "country": "US",
        "quote_currency": "USD",
        "description": "BCH/USD",
        "tenor": "Spot",
        "generic_symbol": "BCHUSD"
      },
      "price_accounts": [
        {
          "account": "5ALDzwcRJfSyGdGyhP3kP628aqBNHZzLuVww7o9kdspe",
          "price_type": "price",
          "price_exponent": -8,
          "status": "trading",
          "price": 60282000000,
          "conf": 26000000,
          "ema_price": 60321475000,
          "ema_confidence": 22504746,
          "valid_slot": 91402601,
          "pub_slot": 91402604,
          "prev_slot": 91402600,
          "prev_price": 60282000000,
          "prev_conf": 26000000,
          "publisher_accounts": [
            {
              "account": "HekM1hBawXQu6wK6Ah1yw1YXXeMUDD2bfCHEzo25vnEB",
              "status": "trading",
              "price": 60282000000,
              "conf": 26000000,
              "slot": 91402599
            },
            {
              "account": "2V7t5NaKY7aGkwytCWQgvUYZfEr9XMwNChhJEakTExk6",
              "status": "unknown",
              "price": 0,
              "conf": 0,
              "slot": 0
            }
          ]
        }
      ]
    },
    {
      "account": "3nuELNFBkbXqsXtnCzphRPCX6toKKYxVDnkyr9pTwB1K",
      "attr_dict": {
        "asset_type": "Crypto",
        "symbol": "SABER/USD",
        "country": "US",
        "quote_currency": "USD",
        "description": "SABER/USD",
        "tenor": "Spot",
        "generic_symbol": "SABERUSD"
      },
      "price_accounts": [
        {
          "account": "8Td9VML1nHxQK6M8VVyzsHo32D7VBk72jSpa9U861z2A",
          "price_type": "price",
          "price_exponent": -8,
          "status": "trading",
          "price": 5785000,
          "conf": 5000,
          "ema_price": 5856365,
          "ema_confidence": 10241,
          "valid_slot": 91402601,
          "pub_slot": 91402604,
          "prev_slot": 91402600,
          "prev_price": 5785000,
          "prev_conf": 5000,
          "publisher_accounts": [
            {
              "account": "GKNcUmNacSJo4S2Kq3DuYRYRGw3sNUfJ4tyqd198t6vQ",
              "status": "trading",
              "price": 5785000,
              "conf": 5000,
              "slot": 91402601
            }
          ]
        }
      ]
    }
  ],
  "id": 1
}
```
