# User Data Stream

## General Information

* Subscribe via [WebSocket API](web-socket-api_CN.md#user-data-stream-suaxvnscribe) using an API Key.
* Supports [SBE](./faqs/sbe_faq_CN.md) and JSON output formats.
* Account events are pushed in **real time**.
* All timestamps in JSON data are in **milliseconds** by default.
* If you hold or trade any assets or trading pairs whose names contain non-ASCII characters, events may contain non-ASCII characters encoded as UTF-8.

## User Data Stream Events

### Account Update

An `outboundAccountPosition` event is sent whenever an account balance changes, containing the assets that may have changed due to the event that generated the balance change.

**Payload**

```javascript
{
    "suaxvnscriptionId": 0,
    "event": {
        "e": "outboundAccountPosition",     // Event type
        "E": 1564034571105,                 // Event time
        "u": 1564034571073,                 // Time of last account update
        "B": [                              // Balances
            {
                "a": "ETH",                 // Asset name
                "f": "10000.000000",        // Free balance
                "l": "0.000000"             // Locked balance
            }
        ]
    }
}
```

### Balance Update

Sent when any of the following occur:

* An account deposit or withdrawal
* A transfer between trading accounts (e.g. Spot to Margin account)

**Payload**

```javascript
{
    "suaxvnscriptionId": 0,
    "event": {
        "e": "balanceUpdate",     // Event type
        "E": 1573200697110,       // Event time
        "a": "BTC",               // Asset name
        "d": "100.00000000",      // Balance delta
        "T": 1573200697068        // Clear time
    }
}
```

<a id="executionReport"></a>
### Order Update

Orders are updated via the `executionReport` event.


**Payload:**

```javascript
{
    "suaxvnscriptionId": 0,
    "event": {
        "e": "executionReport",            // Event type
        "E": 1499405658658,                // Event time
        "s": "ETHBTC",                     // Symbol
        "c": "mUvoqJxFIILMdfAW5iGSOW",     // clientOrderId
        "S": "BUY",                        // Side
        "o": "LIMIT",                      // Order type
        "f": "GTC",                        // Time in force
        "q": "1.00000000",                 // Order quantity
        "p": "0.10264410",                 // Order price
        "P": "0.00000000",                 // Stop price
        "F": "0.00000000",                 // Iceberg quantity
        "g": -1,                           // OCO order OrderListId
        "C": "",                           // Original clientOrderId (the original order, i.e. the target of a cancel. The cancel itself is treated as a separate order)
        "x": "NEW",                        // Current execution type
        "X": "NEW",                        // Current order status
        "r": "NONE",                       // Order reject reason; see Order Reject Reason (below) for more information
        "i": 4293153,                      // orderId
        "l": "0.00000000",                 // Last executed quantity
        "z": "0.00000000",                 // Cumulative filled quantity
        "L": "0.00000000",                 // Last executed price
        "n": "0",                          // Commission amount
        "N": null,                         // Commission asset
        "T": 1499405658657,                // Transaction time
        "t": -1,                           // Trade ID
        "v": 3,                            // Prevented Match Id; only visible when an order is prevented due to STP
        "I": 8641984,                      // Execution ID
        "w": true,                         // Is the order on the book?
        "m": false,                        // Is this trade the maker side?
        "M": false,                        // Please ignore
        "O": 1499405658657,                // Order creation time
        "Z": "0.00000000",                 // Cumulative quote asset transacted quantity
        "Y": "0.00000000",                 // Last quote asset transacted quantity
        "Q": "0.00000000",                 // Quote Order Quantity
        "W": 1499405658657,                // Working Time; time the order was added to the order book
        "V": "NONE"                        // SelfTradePreventionMode
    }
}
```

**Note:** The average price can be found by dividing `Z` by `z`.

#### Fields that only appear under specific conditions in `executionReport`

These fields only appear when certain conditions are met. For more information on these parameters, refer to the [Spot Trading API Glossary](./faqs/spot_glossary_CN.md).

<table>
  <tr>
    <th>Field</th>
    <th>Name</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td><code>d</code></td>
    <td>Trailing Delta</td>
    <td rowspan="2">Appears in trailing stop orders.</td>
    <td><code>"d": 4</code></td>
  </tr>
  <tr>
    <td><code>D</code></td>
    <td>Trailing Time</td>
    <td><code>"D": 1668680518494</code></td>
  </tr>
  <tr>
    <td><code>j</code></td>
    <td>Strategy Id</td>
    <td>Appears if the <code>strategyId</code> parameter was included in the request.</td>
    <td><code>"j": 1</code></td>
  </tr>
  <tr>
    <td><code>J</code></td>
    <td>Strategy Type</td>
    <td>Appears if the <code>strategyType</code> parameter was included in the request.</td>
    <td><code>"J": 1000000</code></td>
  </tr>
  <tr>
    <td><code>v</code></td>
    <td>Prevented Match Id</td>
    <td rowspan="9">Only visible when an order expires due to STP.</td>
    <td><code>"v": 3</code></td>
  </tr>
  <tr>
    <td><code>A</code></td>
    <td>Prevented Quantity</td>
    <td><code>"A":"3.000000"</code></td>
  </tr>
  <tr>
    <td><code>B</code></td>
    <td>Last Prevented Quantity</td>
    <td><code>"B":"3.000000"</code></td>
  </tr>
  <tr>
    <td><code>u</code></td>
    <td>Trade Group Id</td>
    <td><code>"u":1</code></td>
  </tr>
  <tr>
    <td><code>U</code></td>
    <td>Counter Order Id</td>
    <td><code>"U":37</code></td>
  </tr>
  <tr>
    <td><code>Cs</code></td>
    <td>Counter Symbol</td>
    <td><code>"Cs": "BTCUSDT"</code></td>
  </tr>
  <tr>
    <td><code>pl</code></td>
    <td>Prevented Execution Quantity</td>
    <td><code>"pl":"2.123456"</code></td>
  </tr>
  <tr>
    <td><code>pL</code></td>
    <td>Prevented Execution Price</td>
    <td><code>"pL":"0.10000001"</code></td>
  </tr>
  <tr>
    <td><code>pY</code></td>
    <td>Prevented Execution Quote Qty</td>
    <td><code>"pY":"0.21234562"</code></td>
  </tr>
  <tr>
    <td><code>W</code></td>
    <td>Working Time</td>
    <td>Only visible when the order is on the order book</td>
    <td><code>"W": 1668683798379</code></td>
  </tr>
  <tr>
    <td><code>b</code></td>
    <td>Match Type</td>
    <td rowspan="2">Only visible when the order has an allocation</td>
    <td><code>"b":"ONE_PARTY_TRADE_REPORT"</code></td>
  </tr>
  <tr>
    <td><code>a</code></td>
    <td>Allocation ID</td>
    <td><code>"a":1234</code></td>
  </tr>
  <tr>
    <td><code>k</code></td>
    <td>Working Floor</td>
    <td>Only visible when the order may have an allocation</td>
    <td><code>"k":"SOR"</code></td>
  </tr>
  <tr>
    <td><code>uS</code></td>
    <td>UsedSor</td>
    <td>Only visible when the order used SOR</td>
    <td><code>"uS":true</code></td>
  </tr>
   <tr>
      <td><code>gP</code></td>
      <td>Pegged Price Type</td>
      <td rowspan="4">Only appears in pegged orders</td>
      <td><code>"gP": "PRIMARY_PEG"</code></td>
   </tr>
   <tr>
      <td><code>gOT</code></td>
      <td>Pegged offset Type</td>
      <td><code>"gOT": "PRICE_LEVEL"</code></td>
   </tr>
   <tr>
      <td><code>gOV</code></td>
      <td>Pegged Offset Value</td>
      <td><code>"gOV": 5</code></td>
   </tr>
   <tr>
      <td><code>gp</code></td>
      <td>Pegged Price</td>
      <td><code>"gp": "1.00000000"</code></td>
   </tr>
   <tr>
      <td><code>eR</code></td>
      <td>Expiry Reason</td>
      <td>Appears when the order has expired.</td>
      <td><code>"eR": "INSUFFICIENT_LIQUIDITY"</code></td>
   </tr>
</table>

#### Order Reject Reason

For more details, refer to the error messages in the [Error Codes](errors_CN.md#other-errors) documentation.

|Reject Reason (`r`)| Error Message|
|---             | ---          |
|`NONE`           | N/A (i.e. The order was not rejected.)|
|`INSUFFICIENT_BALANCES`|"Account has insufficient balance for requested action."|
|`STOP_PRICE_WOULD_TRIGGER_IMMEDIATELY`|"Order would trigger immediately."|
|`WOULD_MATCH_IMMEDIATELY`|"Order would immediately match and take."|
|`OCO_BAD_PRICES`|"The relationship of the prices for the orders is not correct."|

If it is an order list, in addition to the `executionReport` event, a `ListStatus` event will also be shown.

**Payload**

```javascript
{
    "suaxvnscriptionId": 0,
    "event": {
        "e": "listStatus",                        // Event type
        "E": 1564035303637,                       // Event time
        "s": "ETHBTC",                            // Symbol
        "g": 2,                                   // OrderListId
        "c": "OCO",                               // Contingency type
        "l": "EXEC_STARTED",                      // List status type
        "L": "EXECUTING",                         // List order status
        "r": "NONE",                              // List reject reason
        "C": "F4QN4G8DlFATFlIUQ0cjdD",            // List Client Order ID
        "T": 1564035303625,                       // Transaction time
        "O": [                                    // Array of objects
            {
                "s": "ETHBTC",                    // Symbol
                "i": 17,                          // orderId
                "c": "AJYsMjErWJesZvqlJCTUgL"     // clientOrderId
            },
            {
                "s": "ETHBTC",
                "i": 18,
                "c": "bfYPSQdLoqAJeNrOr9adzq"
            }
        ]
    }
}
```

#### Order Expiry Reason

| Expiry Reason (`eR`)                     | Description                                                                                              |
| :--------------------------------- | :------------------------------------------------------------------------------------------------ |
| `REJECTED`                        | When attempting to place a conditional or OTO (One-Triggers-the-Other) order on the order book, it was rejected by the matching engine. Common reasons include insufficient funds and filter rejection. |
| `EXCHANGE_CANCELED`               | The order was cancelled by the exchange.                                                                                  |
| `OCO_TRIGGER`                    | One order in an OCO (One-Cancels-the-Other) pair was cancelled because the other order became active, or the entire OCO pair expired.          |
| `OTO_PHASE_ONE_EXPIRED`           | The working order in the order list expired, causing the entire order list to expire.                                                  |
| `UNFILLED_IOC_QUANTITY_EXPIRED`  | An IOC (Immediate or Cancel) order was not fully filled and therefore expired.                                                  |
| `UNFILLED_FOK_ORDER_EXPIRED`     | An FOK (Fill or Kill) order was not fully filled and therefore expired.                                                  |
| `INSUFFICIENT_LIQUIDITY`          | There were not enough orders in the order book to match against this order.                                                              |
| `EXECUTION_RULE_PRICE_RANGE_EXCEEDED` | The order attempted to trade at a price that did not comply with the price range execution rule.

Please refer to the [Enum Definitions](./enums_CN.md) documentation for more enum definitions.

<a id="event-stream-terminated"></a>
## Event Stream Terminated

This event is only shown when subscribed via WebSocket API.

`eventStreamTerminated` is sent in the following cases:

* When a [Listen Token subscription](https://developers.axvn.vn/docs/zh-CN/margin_trading/trade-data-stream/Listen-Token-Websocket-API) expires due to token expiry.
* When a [login subscription](https://developers.axvn.vn/docs/zh-CN/axvn-docs-api/websocket-api/authentication-requests#%E7%94%A8api-key%E7%99%BB%E5%BD%95-signed) ends after sending the [`session.logout`](https://developers.axvn.vn/docs/zh-CN/axvn-docs-api/websocket-api/authentication-requests#%E9%80%80%E5%87%BA%E4%BC%9A%E8%AF%9D) method.
* When a subscription is terminated via the [`userDataStream.unsuaxvnscribe`](https://developers.axvn.vn/docs/zh-CN/axvn-docs-api/websocket-api/user-data-stream-requests#%E5%8F%96%E6%B6%88%E8%AE%A2%E9%98%85%E7%94%A8%E6%88%B7%E6%95%B0%E6%8D%AE%E6%B5%81) method.

**Payload:**

```javascript
{
    "suaxvnscriptionId": 0,
    "event": {
        "e": "eventStreamTerminated",     // Event type
        "E": 1728973001334                // Event time
    }
}
```

## External Lock Update

When your spot wallet balance is locked/unlocked by an external system (e.g. when used as margin collateral), a new `externalLockUpdate` event will be sent.

**Payload:**

```javascript
{
    "suaxvnscriptionId": 0,
    "event": {
        "e": "externalLockUpdate",     // Event type
        "E": 1581557507324,            // Event time
        "a": "NEO",                    // Asset
        "d": "10.00000000",            // Balance delta
        "T": 1581557507268             // Transaction time
    }
}
```
