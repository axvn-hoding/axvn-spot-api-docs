# SBE Market Data Streams

## WSS Basic Information

* The base URL is **stream-sbe.axvn.vn** or **stream-sbe.axvn.vn:9443**.
* To retrieve market data in JSON format, see [this page](web-socket-streams_CN.md).
* The SBE schema used for decoding streams can be found [here](https://github.com/nham-quoc-huan/axvn-docs-api/blob/master/sbe/schemas/stream_1_0.xml).
* All trading pair names in stream names are **lowercase**.
* Example URL format for subscribing to a single stream: **/ws/\<streamName\>**.
* Example URL format for subscribing to combined streams: **/stream?streams=\<streamName1\>/\<streamName2\>/\<streamName3\>**.
* Each connection to **stream-sbe.axvn.vn** is valid for no more than 24 hours; handle reconnections appropriately.
* When the server is about to shut down, it will send a [`serverShutdown`](#serverShutdown) event and disconnect the connection. Please establish a new connection as quickly as possible to prevent interruption.
* All time and timestamp-related fields are in **microseconds**.
* **API Key authentication is required.**
  * Only Ed25519 keys are supported.
  * When opening a connection, include your API Key in the `X-MBX-APIKEY` header. Timestamp and signature are not required.
  * No additional API key permissions are needed to access public market data. Trading pair whitelisting also does not affect access to SBE market data streams.
  * However, if an API key uses IP whitelisting, only the specified IP addresses are permitted to use the API key.
* The WebSocket server sends a PING frame **every 20 seconds**.
  * If the WebSocket server does not receive a PONG frame response within one minute, the connection will be disconnected.
  * When a client receives a PING frame, it must reply with a PONG frame as quickly as possible, with the same payload as the PING frame.
  * The server allows unsolicited PONG frames, but this does not guarantee the connection will not be disconnected. **For this type of PONG frame, it is recommended to set the payload to empty.**
* [Live subscribe and unsubscribe is supported](web-socket-streams_CN.md#live-subscribing-unsubscribing-to-streams).
  * You must send subscription requests via WebSocket text frames in JSON format; subscription responses will also be returned via WebSocket text frames in JSON format.
  * [`serverShutdown`](#serverShutdown) events will be sent via WebSocket text frames in JSON format.
  * Market data events will be sent via WebSocket binary frames in SBE format.
* If your request contains a trading pair name with non-ASCII characters, the stream events may contain non-ASCII characters encoded as UTF-8.

## WebSocket Connection Limits

* The WebSocket server **accepts a maximum of 5 messages per second**.
  * Messages include:
    * PING frames
    * PONG frames
    * Text frame JSON control requests
  * Events pushed by the server are not subject to rate limits.
  * If a user sends more messages than the limit, the connection will be disconnected. IPs that are repeatedly disconnected may be blocked by the server.
* A single connection can subscribe to a maximum of 1024 streams.
* The request limit per IP address is **no more than 300 connection requests per 5 minutes**.

<a id="serverShutdown"></a>
## Server Shutdown

When the server is about to shut down, a `serverShutdown` event will be sent.

Note that this message will be sent in JSON format.

```javascript
{
  "e": "serverShutdown", // Event type
  "E": 1770123456789     // Event time
}
```

Please establish a new connection as quickly as possible to prevent interruption.

## Available Streams

### Individual Symbol Trade Streams

Raw trade information pushed in real time.

**SBE Message Name:** `TradesStreamEvent`

**Stream Name**: \<symbol\>@trade

**Update Speed:** Real-time

### Best Bid/Ask Streams

Pushes the best bid price, ask price, and their quantities in real time when the order book changes.

> [!NOTE]
> The best bid/ask stream in SBE is equivalent to the `bookTicker` stream in JSON format, except that it supports auto-culling and also includes the `eventTime` field.

**SBE Message Name:** `BestBidAskStreamEvent`

**Stream Name**: \<symbol\>@bestBidAsk

**Update Speed**: Real-time

<a id="auto-culling"></a>
The SBE best bid/ask stream uses **auto-culling**: when system load is high, outdated events may be dropped instead of being queued and sent with a delay.

For example, if a best bid/ask event is generated at time T2 and there is still an unsent event queued from time T1 (where T1 < T2), the T1 event will be dropped and only the T2 event will be sent. This is performed on a per-symbol basis.

<a id="diff-depth-streams"></a>
### Diff. Depth Stream

Periodically pushes incremental updates to the order book. Use this stream to maintain a local order book.

[How to manage a local order book.](web-socket-streams_CN.md#how-to-manage-a-local-order-book-correctly)

**SBE Message Name:** `DepthDiffStreamEvent`

**Stream Name**: \<symbol\>@depth

**Update Speed:** 20ms

### Partial Book Depth Streams

A snapshot of the top 20 order book levels, pushed periodically.

**SBE Message Name:** `DepthSnapshotStreamEvent`

**Stream Name**: \<symbol\>@depth20

**Update Speed:** 50ms
