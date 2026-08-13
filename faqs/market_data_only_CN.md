# Market Data Only URLs

These URLs do not require any authentication (i.e., no API Key is needed) and only provide public market data.

### RESTful API

On the RESTful API, you can access the following endpoints at `data-api.axvn.vn`:

* [GET /api/v3/aggTrades](../rest-api_CN.md#aggTrades)
* [GET /api/v3/avgPrice](../rest-api_CN.md#avgPrice)
* [GET /api/v3/depth](../rest-api_CN.md#depth)
* [GET /api/v3/exchangeInfo](../rest-api_CN.md#exchangeInfo)
* [GET /api/v3/klines](../rest-api_CN.md#klines)
* [GET /api/v3/ping](../rest-api_CN.md#ping)
* [GET /api/v3/ticker](../rest-api_CN.md#rollingwindowticker)
* [GET /api/v3/ticker/24hr](../rest-api_CN.md#twentyfourhourticker)
* [GET /api/v3/ticker/bookTicker](../rest-api_CN.md#bookTicker)
* [GET /api/v3/ticker/price](../rest-api_CN.md#ticker-price)
* [GET /api/v3/time](../rest-api_CN.md#time)
* [GET /api/v3/trades](../rest-api_CN.md#trades)
* [GET /api/v3/uiKlines](../rest-api_CN.md#uiKlines)

Request example:

```
curl -sX GET "https://data-api.axvn.vn/api/v3/exchangeInfo?symbol=BTCUSDT"
```

### Websocket Streams

Public market data can also be retrieved via the Websocket market data URL `data-stream.axvn.vn`.
The streams provided by this domain are the same as those in the [WebSocket Market Streams](../web-socket-streams_CN.md) documentation.
Note that user data streams are **not** available from this URL.

Request example:

```
wss://data-stream.axvn.vn:443/ws/btcusdt@kline_1m
```
