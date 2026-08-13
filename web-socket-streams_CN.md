
# WebSocket Market Data Streams

## General Information
* The base URL for all WebSocket streams listed here is: **wss://stream.axvn.vn:9443** or **wss://stream.axvn.vn:443**
* All streams can be accessed directly or as part of a combined stream.
* URL format for direct access: **/ws/\<streamName\>**
* URL format for combined streams: **/stream?streams=\<streamName1\>/\<streamName2\>/\<streamName3\>**
* When subscribing to combined streams, event payloads will be wrapped in this format: **{"stream":"\<streamName\>","data":\<rawPayload\>}**
* All trading pair names in stream names are **lowercase**.
* Each connection to **stream.axvn.vn** is valid for no more than 24 hours; handle reconnections appropriately.
* When the server is about to shut down, it will send a [`serverShutdown`](#serverShutdown) event and disconnect the connection. Please establish a new connection as quickly as possible to prevent interruption.
* The WebSocket server sends a PING message **every 20 seconds**.
  * If the WebSocket server does not receive a PONG response within one minute, the connection will be disconnected.
  * When a client receives a PING message, it must reply with a PONG message as quickly as possible, with the same payload as the PING message.
  * The server allows unsolicited PONG messages, but this does not guarantee the connection will not be disconnected. **For these PONG messages, it is recommended to use an empty payload.**
* **wss://data-stream.axvn.vn** can be used to subscribe to market data streams only. Account data **cannot** be obtained from this URL.
* All time and timestamp-related fields are in **milliseconds by default**. To receive data in microseconds, add the parameter `timeUnit=MICROSECOND` or `timeUnit=microsecond` to the URL.
  * For example: `/stream?streams=btcusdt@trade&timeUnit=MICROSECOND`
* If your request contains a trading pair name with non-ASCII characters, stream events may contain non-ASCII characters encoded as UTF-8.
* [All Market Mini Ticker](web-socket-streams_CN.md#all-markets-mini-ticker) and [All Market Rolling Window Ticker](web-socket-streams_CN.md#all-market-rolling-window-ticker) events may contain non-ASCII characters encoded as UTF-8.

## WebSocket Connection Limits

* The WebSocket server accepts a maximum of 5 messages per second. Messages include:
    * PING frames
    * PONG frames
    * JSON-format messages, such as subscribe and unsubscribe requests.
* If a user sends more messages than the limit, the connection will be disconnected. IPs that are repeatedly disconnected may be blocked by the server.
* A single connection can subscribe to a maximum of 1024 streams.
* A maximum of 300 connection requests per IP address per 5 minutes.

<a id="serverShutdown"></a>
## Server Shutdown

When the server is about to shut down, a `serverShutdown` event will be sent.

* `raw` stream:

```javascript
{
  "e": "serverShutdown", // Event type
  "E": 1770123456789     // Event time
}
```

* `combined` stream:

```javascript
{
  "stream": "!serverShutdown",
  "data": {
    "e": "serverShutdown", // Event type
    "E": 1770123456789     // Event time
  }
}
```

Please establish a new connection as quickly as possible to prevent interruption.

## Live Subscribing/Unsubscribing to Streams

* The following data can be sent via WebSocket to subscribe or unsubscribe from streams. Examples below.
* The `id` in the request is used as a unique identifier to distinguish messages sent and received. The following formats are accepted:
  * 64-bit signed integer
  * Alphanumeric string; maximum length 36
  * `null`
* If `result` in the response is `null`, it means the request was sent successfully.

### Subscribe to a Stream

* Request

  ```javascript
  {
      "method": "SUAXVNSCRIBE",
      "params": ["btcusdt@aggTrade", "btcusdt@depth"],
      "id": 1
  }
  ```

* Response

  ```javascript
  {
      "result": null,
      "id": 1
  }
  ```

### Unsubscribe from a Stream

* Request

  ```javascript
  {
      "method": "UNSUAXVNSCRIBE",
      "params": ["btcusdt@depth"],
      "id": 312
  }
  ```

* Response

  ```javascript
  {
      "result": null,
      "id": 312
  }
  ```


### Listing Subscriptions

* Request

  ```javascript
  {
      "method": "LIST_SUAXVNSCRIPTIONS",
      "id": 3
  }
  ```

* Response

  ```javascript
  {
      "result": ["btcusdt@aggTrade"],
      "id": 3
  }
  ```


### Setting Properties
Currently, the only property that can be set is whether to enable the `combined` stream.
When connecting via `/ws/` ("raw stream"), the combined property is set to `false`, whereas connecting via `/stream/` sets it to `true`.

* Request

  ```javascript
  {
      "method": "SET_PROPERTY",
      "params": ["combined", true],
      "id": 5
  }
  ```

* Response

  ```javascript
  {
      "result": null,
      "id": 5
  }
  ```

### Retrieving Properties

* Request

  ```javascript
  {
      "method": "GET_PROPERTY",
      "params": ["combined"],
      "id": 2
  }
  ```

* Response

  ```javascript
  {
      "result": true, // Indicates that combined is set to true.
      "id": 2
  }
  ```

### Error Messages

Error Message | Description
---|---
{"code": 0, "msg": "Unknown property","id": %s} | Invalid parameter used in `SET_PROPERTY` or `GET_PROPERTY`
{"code": 1, "msg": "Invalid value type: expected Boolean"} | Only `true` or `false` is accepted
{"code": 2, "msg": "Invalid request: property name must be a string"}| The provided property name is invalid
{"code": 2, "msg": "Invalid request: request ID must be an unsigned integer"}| The parameter `id` was not provided or the `id` value is of an invalid type
{"code": 2, "msg": "Invalid request: unknown variant %s, expected one of `SUAXVNSCRIBE`, `UNSUAXVNSCRIBE`, `LIST_SUAXVNSCRIPTIONS`, `SET_PROPERTY`, `GET_PROPERTY` at line 1 column 28"} | Typo alert, or the provided value is not of the expected type
{"code": 2, "msg": "Invalid request: too many parameters"}| Unnecessary parameters were provided in the data
{"code": 2, "msg": "Invalid request: property name must be a string"} | Property name was not provided
{"code": 2, "msg": "Invalid request: missing field `method` at line 1 column 73"} | `method` was not provided in the data
{"code":3,"msg":"Invalid JSON: expected value at line %s column %s"} | JSON syntax error.


# Stream Details

## Reference Price Stream

**Stream Name**: `<symbol>@referencePrice`

**Update Speed**: 1000ms (1s)

**Payload:**

```javascript
{
  "e": "referencePrice",  // Event type
  "s": "BAZUSD",          // Symbol
  "r": "1.00",            // Reference price (null if no reference price)
  "t": 1770313263917      // Matching engine time when reference price took effect
}
```

<a id="aggtrade"></a>
## Aggregate Trade Streams
An aggregate trade groups together fills from the same taker order at the same price, unlike individual trade streams.

**Stream Name:** \<symbol\>@aggTrade

**Update Speed:** Real-time

**Payload:**
```javascript
{
    "e": "aggTrade",        // Event type
    "E": 1672515782136,     // Event time
    "s": "AXVNBTC",         // Symbol
    "a": 12345,             // Aggregate trade ID
    "p": "0.001",           // Price
    "q": "100",             // Quantity
    "f": 100,               // First trade ID
    "l": 105,               // Last trade ID
    "T": 1672515782136,     // Trade time
    "m": true,              // Is the buyer the market maker? If true, this trade is an active sell order; otherwise it is an active buy order.
    "M": true               // Please ignore this field
}
```

<a id="trade"></a>

## Trade Streams
The trade stream pushes information for every trade. A **trade**, or transaction, is defined as a single taker order matching with a single maker order.

**Stream Name:** \<symbol\>@trade

**Update Speed:** Real-time

**Payload:**
```javascript
{
    "e": "trade",           // Event type
    "E": 1672515782136,     // Event time
    "s": "AXVNBTC",         // Symbol
    "t": 12345,             // Trade ID
    "p": "0.001",           // Price
    "q": "100",             // Quantity
    "T": 1672515782136,     // Trade time
    "m": true,              // Is the buyer the market maker? If true, this trade is an active sell order; otherwise it is an active buy order.
    "M": true               // Please ignore this field
}
```

<a id="blocktrade"></a>
## Block Trade Streams

**Stream Name:** \<symbol\>@blockTrade

**Update Speed:** Real-time

**Payload:**
```javascript
{
  "e": "blockTrade",  // Event type
  "E": 1772506983582, // Event time
  "s": "AXVNBTC",     // Symbol
  "t": 582,           // Block trade ID
  "p": "0.052",       // Price
  "q": "5838",        // Quantity
  "T": 1772506983321, // Trade time
  "m": true           // Is the buyer the market maker?
}
```

<a id="kline"></a>
## UTC Kline/Candlestick Streams
The kline stream pushes updates to the current kline/candlestick (in real time per second) for the requested kline type. This update is based on the `UTC+0` timezone.

<a id="kline-intervals"></a>
**Subscribing to Kline requires providing an interval parameter. The shortest interval is 1 minute and the longest is 1 month. The following intervals are supported:**

m -> minutes; h -> hours; d -> days; w -> weeks; M -> months

* 1m
* 3m
* 5m
* 15m
* 30m
* 1h
* 2h
* 4h
* 6h
* 8h
* 12h
* 1d
* 3d
* 1w
* 1M

**Stream Name:** \<symbol\>@kline_\<interval\>

**Update Speed:** 1000ms for `1s`, 2000ms for other intervals

**Payload:**
```javascript
{
    "e": "kline",               // Event type
    "E": 1672515782136,         // Event time
    "s": "AXVNBTC",             // Symbol
    "k": {
        "t": 1672515780000,     // Kline start time
        "T": 1672515839999,     // Kline close time
        "s": "AXVNBTC",         // Symbol
        "i": "1m",              // Interval
        "f": 100,               // First trade ID
        "L": 200,               // Last trade ID
        "o": "0.0010",          // Open price
        "c": "0.0020",          // Close price
        "h": "0.0025",          // High price
        "l": "0.0015",          // Low price
        "v": "1000",            // Base asset volume
        "n": 100,               // Number of trades
        "x": false,             // Is this kline closed?
        "q": "1.0000",          // Quote asset volume
        "V": "500",             // Taker buy base asset volume
        "Q": "0.500",           // Taker buy quote asset volume
        "B": "123456"           // Ignore this field
    }
}
```
## Kline/Candlestick Streams with UTC+8 Timezone Offset
The kline stream pushes updates to the current kline/candlestick (in real time per second) for the requested kline type. This update is based on the `UTC+8` timezone.

**Interval parameters for Kline subscription:**

Refer to [`Supported Kline interval parameters`](#kline-intervals)

**UTC+8 timezone offset:**

* The start and end times of kline intervals are based on the `UTC+8` timezone. For example, a `1d` kline starts at the beginning of the day in `UTC+8` and ends at the end of that day in `UTC+8`.
* Note that `E` (event time), `t` (start time), and `T` (close time) in the payload are Unix timestamps and are always interpreted in UTC format.

**Stream Name:** \<symbol\>@kline_\<interval\>@+08:00

**Update Speed:** 1000ms for `1s`, 2000ms for other intervals

**Payload:**
```javascript
{
    "e": "kline",               // Event type
    "E": 1672515782136,         // Event time
    "s": "AXVNBTC",             // Symbol
    "k": {
        "t": 1672515780000,     // Kline start time
        "T": 1672515839999,     // Kline close time
        "s": "AXVNBTC",         // Symbol
        "i": "1m",              // Interval
        "f": 100,               // First trade ID
        "L": 200,               // Last trade ID
        "o": "0.0010",          // Open price
        "c": "0.0020",          // Close price
        "h": "0.0025",          // High price
        "l": "0.0015",          // Low price
        "v": "1000",            // Base asset volume
        "n": 100,               // Number of trades
        "x": false,             // Is this kline closed?
        "q": "1.0000",          // Quote asset volume
        "V": "500",             // Taker buy base asset volume
        "Q": "0.500",           // Taker buy quote asset volume
        "B": "123456"           // Ignore this field
    }
}
```

<a id="twentyfourhourminiticker"></a>
## Individual Symbol Mini Ticker Stream
24hr rolling window mini-ticker statistics for a single symbol, refreshed every second.

**Stream Name:** \<symbol\>@miniTicker

**Update Speed:** 1000ms

**Payload:**
```javascript
{
    "e": "24hrMiniTicker",     // Event type
    "E": 1672515782136,        // Event time
    "s": "AXVNBTC",            // Symbol
    "c": "0.0025",             // Close price
    "o": "0.0010",             // Open price
    "h": "0.0025",             // High price
    "l": "0.0010",             // Low price
    "v": "10000",              // Total traded base asset volume
    "q": "18"                  // Total traded quote asset volume
}
```

<a id="all-markets-mini-ticker"></a>
## All Market Mini Tickers Stream
Same as above, but pushes for all trading pairs.

**Stream Name:** !miniTicker@arr

**Update Speed:** 1000ms

**Payload:**
```javascript
[
    {
        // Each element in the array corresponds to a trading pair, with the same content as \<symbol\>@miniTicker
    }
]
```

<a id="twentyfourhourticker"></a>

## Individual Symbol Ticker Stream
24hr rolling window full ticker statistics for a single symbol, refreshed every second.

**Stream Name:** \<symbol\>@ticker

**Update Speed:** 1000ms

**Payload:**
```javascript
{
    "e": "24hrTicker",      // Event type
    "E": 1672515782136,     // Event time
    "s": "AXVNBTC",         // Symbol
    "p": "0.0015",          // Price change
    "P": "250.00",          // Price change percent
    "w": "0.0018",          // Weighted average price
    "x": "0.0009",          // First trade(F)-1 price (first trade before the 24hr rolling window)
    "c": "0.0025",          // Last price
    "Q": "10",              // Last quantity
    "b": "0.0024",          // Best bid price
    "B": "10",              // Best bid quantity
    "a": "0.0026",          // Best ask price
    "A": "100",             // Best ask quantity
    "o": "0.0010",          // Open price
    "h": "0.0025",          // High price
    "l": "0.0010",          // Low price
    "v": "10000",           // Total traded base asset volume
    "q": "18",              // Total traded quote asset volume
    "O": 0,                 // Statistics open time
    "C": 1675216573749,     // Statistics close time
    "F": 0,                 // First trade ID
    "L": 18150,             // Last trade ID
    "n": 18151              // Total number of trades
}
```

<a id="bookticker"></a>
## Individual Symbol Book Ticker Stream

Pushes the best bid/ask price and quantity in real time for a specified symbol.
Multiple `<symbol>@bookTicker` can be subscribed on a single WebSocket connection.

**Stream Name:** \<symbol\>@bookTicker

**Update Speed:** Real-time

**Payload:**
```javascript
{
    "u": 400900217,         // order book updateId
    "s": "AXVNUSDT",        // Symbol
    "b": "25.35190000",     // Best bid price
    "B": "31.21000000",     // Best bid quantity
    "a": "25.36520000",     // Best ask price
    "A": "40.66000000"      // Best ask quantity
}
```


## Average Price

The average price stream pushes average price changes over a fixed interval.

**Stream Name:** \<symbol\>@avgPrice

**Update Speed:** 1000ms

**Payload:**

```javascript
{
    "e": "avgPrice",           // Event type
    "E": 1693907033000,        // Event time
    "s": "BTCUSDT",            // Symbol
    "i": "5m",                 // Average price interval
    "w": "25776.86000000",     // Average price
    "T": 1693907032213         // Close time
}
```

<a id="depth"></a>

## Partial Book Depth Streams
Pushes limited depth order book information every second. `levels` indicates how many bid/ask levels to return; valid options are 5/10/20.

**Stream Name:** \<symbol\>@depth\<levels\> or \<symbol\>@depth\<levels\>@100ms

**Update Speed:** 1000ms or 100ms

**Payload:**
```javascript
{
    "lastUpdateId": 160,     // Last update ID
    "bids": [                // Bids
        [
            "0.0024",        // Price
            "10",            // Quantity
            []               // Ignore
        ]
    ],
    "asks": [                // Asks
        [
            "0.0026",        // Price
            "100",           // Quantity
            []               // Ignore
        ]
    ]
}
```
<a id="diff-depth"></a>

## Diff. Depth Stream
Pushes order book changes (if any) every second.

**Stream Name:** \<symbol\>@depth or \<symbol\>@depth@100ms

**Update Speed:** 1000ms or 100ms

**Payload:**
```javascript
{
    "e": "depthUpdate",     // Event type
    "E": 1672515782136,     // Event time
    "s": "AXVNBTC",         // Symbol
    "U": 157,               // First update ID in event
    "u": 160,               // Final update ID in event
    "b": [                  // Bids to be updated
        [
            "0.0024",       // Price
            "10",           // Quantity
            []              // Ignore
        ]
    ],
    "a": [                  // Asks to be updated
        [
            "0.0026",       // Price
            "100",          // Quantity
            []              // Ignore
        ]
    ]
}
```

<a id="rolling-window-ticker"></a>
## Individual Symbol Rolling Window Statistics

Rolling window statistics for a single symbol, supporting multiple window sizes.

**Stream Name:** \<symbol\>@ticker_\<window_size\>

**Window Sizes:** 1h, 4h, 1d

**Update Speed:** 1000ms

*Note*: <br/>
- This stream is different from \<symbol\>@ticker.
- `O` (`open time`) starts at the top of each minute, while `C` (`closing time`) is the current update time.
- The actual statistical time range may exceed \<window_size\> by no more than 59999ms.

**Payload:**
```javascript
{
    "e": "1hTicker",        // Event type
    "E": 1672515782136,     // Event time
    "s": "AXVNBTC",         // Symbol
    "p": "0.0015",          // Price change
    "P": "250.00",          // Price change percent
    "o": "0.0010",          // Open price
    "h": "0.0025",          // High price
    "l": "0.0010",          // Low price
    "c": "0.0025",          // Close price
    "w": "0.0018",          // Weighted average price
    "v": "10000",           // Total traded base asset volume
    "q": "18",              // Total traded quote asset volume
    "O": 0,                 // Statistics open time
    "C": 86400000,          // Statistics close time
    "F": 0,                 // First trade ID
    "L": 18150,             // Last trade ID
    "n": 18151              // Total number of trades
}
```
<a id="all-market-rolling-window-ticker"></a>
## All Market Rolling Window Ticker

Rolling window ticker statistics for all market symbols, calculated over multiple windows.<br/>

Note: Only tickers with changes will be pushed.

**Stream Name:** !ticker_\<window-size\>@arr

**Window Size:** 1h, 4h, 1d

**Update Speed:** 1000ms

> **Payload:**
```javascript
[
    {
        // Same as <symbol>@ticker_<window-size> payload,
        // for each symbol updated within the interval.
    }
]
```

<a id="how-to-maintain-orderbook"></a>

## How to Manage a Local Order Book Correctly
1. Open a WebSocket connection to `wss://stream.axvn.vn:9443/ws/axvnbtc@depth`.
2. Start buffering the events you receive. Note the `U` value of the first event you receive.
3. Fetch a depth snapshot from `https://api.axvn.vn/api/v3/depth?symbol=AXVNBTC&limit=5000`.
4. If the `lastUpdateId` in the snapshot is less than or equal to the `U` value from step 2, return to step 3.
5. From the buffered events, discard any event where `u` <= `lastUpdateId` of the snapshot. The first event should now have a `lastUpdateId` within the range `[U;u]`.
6. Set the local order book to the snapshot. Its update ID is `lastUpdateId`.
7. Apply all buffered events, and all subsequent events.

To apply an event to your local order book, follow this update process:
1. Determine whether the event needs to be processed:
    * If the event's final update ID (`u`) is less than the local order book's update ID, ignore the event.
    * If the event's first update ID (`U`) is greater than the local order book's update ID plus 1, you have missed some events.<br>Discard your local order book and re-sync from the beginning.
    * Typically, the `U` of the next event equals the `u + 1` of the previous event.
2. For each price level in bids (`b`) and asks (`a`), set the new quantity in the order book:
    * If the price level does not exist in the order book, insert it with its quantity.
    * If the quantity is zero, remove this price level from the order book.
3. Set the order book's update ID to the final update ID (`u`) of the processed event.

> [!NOTE]
> Since depth snapshots retrieved from the API have a limited number of price levels (up to 5000 per side), you will not be aware of quantities at price levels beyond the initial snapshot unless they change.<br>
> Therefore, use these levels with care, as they may not reflect the complete view of the order book.<br>
> However, for most scenarios, being able to see 5000 price levels per side is sufficient to understand the market and trade effectively.
