# Self Trade Prevention (STP) FAQ

**Disclaimer:**

* The commissions and prices used here are fictional and do not reflect actual exchange settings.

### What is Self Trade Prevention (STP)?

Self Trade Prevention refers to preventing orders from trading against orders from the same account or accounts with the same `tradeGroupId`.

### What is a self-trade?

A self-trade may occur in any of the following situations:

* Orders belonging to the same account trade against each other.
* Orders from accounts with the same `tradeGroupId` trade against each other.

### What happens when STP is triggered?

If an order would trigger a self-trade, the system will apply one of five possible modes:

`NONE` - This mode exempts the order from self-trade prevention.

`EXPIRE_TAKER` - This mode prevents the trade by immediately expiring the remaining quantity of the taker order.

`EXPIRE_MAKER` - This mode prevents the trade by immediately expiring the remaining quantity of the potential maker order.

`EXPIRE_BOTH` - This mode prevents the trade by immediately expiring the remaining quantity of both the taker and maker orders.

`DECREMENT`  - This mode increases the `prevented quantity` of *both* orders by the matched quantity. This will expire the order with the smaller available quantity. If both orders have equal available quantities, both orders will expire.

`TRANSFER` - If the orders are from the same account, the behavior is the same as `DECREMENT`.
If the orders are from different accounts with the same `tradeGroupId`, in addition to the `DECREMENT` behavior, the `last prevented quantity` and its notional value will be transferred between the two accounts.

STP behavior is generally determined only by the **taker's** STP mode. The exception is that STP `TRANSFER` only occurs when both the maker and taker specify `TRANSFER` as the STP mode. If the taker specifies `TRANSFER` but the maker specifies a different STP mode, the STP behavior is `DECREMENT`.

Summary:

| Taker STP Mode | Maker STP Mode | Effective STP Mode |
| :---- | :---- | :---- |
| `TRANSFER` | `TRANSFER` | `TRANSFER` |
| `TRANSFER` | `EXPIRE_MAKER`, `EXPIRE_TAKER`, `EXPIRE_BOTH`, `NONE`, `DECREMENT` | `DECREMENT` |
| `EXPIRE_MAKER`, `EXPIRE_TAKER`, `EXPIRE_BOTH`, `NONE`, `DECREMENT` | Any STP mode | Taker's STP mode |

### What is a Trade Group Id?

Accounts belonging to the same `tradeGroupId` are considered part of the same trading group. Orders submitted by members of the same trading group are eligible for STP.

Each account can confirm whether it belongs to the same `tradeGroupId` via `GET /api/v3/account` (REST API) or `account.status` (WebSocket API).

`tradeGroupId` is also present in the response of `GET /api/v3/preventedMatches` (REST API) or `myPreventedMatches` (WebSocket API).

If the value is `-1`, it indicates the account has no `tradeGroupId` set, so STP can only occur between orders of the same account.

### What is a Prevented Match?

When one or more orders expire due to STP, a prevented match is created.

When a self-trade is prevented, a prevented match is created. Orders involved in the prevented match have their `prevented quantity` increased, which may cause one or more orders to expire.

You can query prevented matches via REST API `GET /api/v3/preventedMatches` or WebSocket API `myPreventedMatches`.

Example response:

```javascript
[
    {
        "symbol": "BTCDUSDT",                         // Trading pair
        "preventedMatchId": 8,                        // ID of the prevented match
        "takerOrderId": 12,                           // Order ID of the taker
        "makerOrderId": 10,                           // Order ID of the maker
        "tradeGroupId": 1,                            // Trade group ID (-1 if account has no trade group)
        "selfTradePreventionMode": "EXPIRE_BOTH",     // The STP mode that caused orders to expire.
        "price": "50.00000000",                       // Price of the matched trade.
        "takerPreventedQuantity": "1.00000000",       // Remaining quantity of the taker order before STP. Present only when STP mode is EXPIRE_TAKER, EXPIRE_BOTH, or DECREMENT.
        "makerPreventedQuantity": "10.00000000",      // Remaining quantity of the maker order before STP. Present only when STP mode is EXPIRE_MAKER, EXPIRE_BOTH, or DECREMENT.
        "transactTime": 1663190634060                 // Time the order expired due to STP.
    }
]
```

### What is "prevented quantity"?

STP events cause the quantity of an order to expire. The `EXPIRE_TAKER`, `EXPIRE_MAKER`, and `EXPIRE_BOTH` modes will expire all remaining quantity of the affected order, thereby expiring the entire order.


`Prevented quantity` represents the quantity of an order that has expired due to an STP event. The User Data Stream may include the following two fields:

```javascript
{
    "A": "3.000000",     // Prevented Quantity (cumulative)
    "B": "3.000000"      // Last Prevented Quantity (from this STP event)
}
```

`B` represents the `TRADE_PREVENTION` execution type, and its value indicates the quantity of the order that expired in this STP event.

`A` represents the cumulative quantity of an order that has expired due to STP events. For `EXPIRE_TAKER`, `EXPIRE_MAKER`, and `EXPIRE_BOTH` modes, its value is always the same as `B`.

The API response for orders that expired due to STP will also have a `preventedQuantity` field indicating the cumulative quantity that expired due to STP.

If the order is in a resting state, the following formula applies:

```
original order quantity - executed quantity - prevented quantity = quantity available for further execution
original order quantity - executed quantity - prevented quantity = quantity available for further execution
```

When an order's available quantity reaches 0, it is removed from the order book. The status will be one of `EXPIRED_IN_MATCH`, `FILLED`, or `EXPIRED`.

### How do I know which trading pairs support STP?

A trading pair can be configured to allow different sets of STP modes and to use different default STP modes.

`defaultSelfTradePreventionMode` - If the user does not provide this when placing an order, the order will use this STP mode.

`allowedSelfTradePreventionModes` - The set of STP modes allowed for orders on this trading pair.

For example, if the trading pair has the following configuration:

```json
{
    "defaultSelfTradePreventionMode": "NONE",
    "allowedSelfTradePreventionModes": ["NONE", "EXPIRE_TAKER", "EXPIRE_BOTH"]
}
```

This means that if the user sends an order without providing `selfTradePreventionMode`, the order will have a value of `NONE`.

If the user wants to explicitly specify a mode, they can pass `NONE`, `EXPIRE_TAKER`, or `EXPIRE_BOTH`.

If the user attempts to specify `EXPIRE_MAKER` for orders on this trading pair, they will receive an error message:

```json
{
    "code": -1013,
    "msg": "This symbol does not allow the specified self-trade prevention mode."
}
```

### How do I know if an order expired due to STP?

The order's status will be `EXPIRED_IN_MATCH`.

### Examples of STP

Assume all orders in the following examples are sent from the same account.

**Case A - User sends a new order with selfTradePreventionMode:`NONE` that would match with an existing order on the book.**


```
Maker order: symbol=BTCUSDT side=BUY type=LIMIT quantity=1 price=1 selfTradePreventionMode=NONE
Taker order: symbol=BTCUSDT side=SELL type=LIMIT quantity=1 price=1 selfTradePreventionMode=NONE
```

**Result:** No STP is triggered; the orders will match.

Maker order status

```json
{
    "symbol": "BTCUSDT",
    "orderId": 2,
    "orderListId": -1,
    "clientOrderId": "FaDk4LPRxastaICEFE9YTf",
    "price": "1.000000",
    "origQty": "1.000000",
    "executedQty": "1.000000",
    "cummulativeQuoteQty": "1.000000",
    "status": "FILLED",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1670217090310,
    "updateTime": 1670217090330,
    "isWorking": true,
    "workingTime": 1670217090310,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "NONE"
}
```

Taker order status

```json
{
    "symbol": "BTCUSDT",
    "orderId": 3,
    "orderListId": -1,
    "clientOrderId": "Ay48Vtpghnsvy6w8RPQEde",
    "transactTime": 1670207731263,
    "price": "1.000000",
    "origQty": "1.000000",
    "executedQty": "1.000000",
    "cummulativeQuoteQty": "1.000000",
    "status": "FILLED",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "SELL",
    "workingTime": 1670207731263,
    "fills": [
        {
            "price": "1.000000",
            "qty": "1.000000",
            "commission": "0.000000",
            "commissionAsset": "USDT",
            "tradeId": 1
        }
    ],
    "selfTradePreventionMode": "NONE"
}
```


**Case B - User sends an order with `EXPIRE_MAKER` that would match with existing orders on the book.**

```
Maker order 1: symbol=BTCUSDT side=BUY type=LIMIT quantity=1.2 price=1.2 selfTradePreventionMode=NONE
Maker order 2: symbol=BTCUSDT side=BUY type=LIMIT quantity=1.3 price=1.1 selfTradePreventionMode=NONE
Maker order 3: symbol=BTCUSDT side=BUY type=LIMIT quantity=8.1 price=1   selfTradePreventionMode=NONE
Taker order 1: symbol=BTCUSDT side=SELL type=LIMIT quantity=3 price=1    selfTradePreventionMode=EXPIRE_MAKER
```

**Result:** Due to STP, the orders on the book will expire, and the taker order will remain on the book.

Maker order 1
```json
{
    "symbol": "BTCUSDT",
    "orderId": 2,
    "orderListId": -1,
    "clientOrderId": "wpNzhSclc16pV8g5THIOR3",
    "price": "1.200000",
    "origQty": "1.200000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "EXPIRED_IN_MATCH",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1670217957437,
    "updateTime": 1670217957498,
    "isWorking": true,
    "workingTime": 1670217957437,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "NONE",
    "preventedMatchId": 0,
    "preventedQuantity": "1.200000"
}
```

Maker order 2

```json
{
    "symbol": "BTCUSDT",
    "orderId": 3,
    "orderListId": -1,
    "clientOrderId": "ZT9emqia99V7x8B6FW0pFF",
    "price": "1.100000",
    "origQty": "1.300000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "EXPIRED_IN_MATCH",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1670217957458,
    "updateTime": 1670217957498,
    "isWorking": true,
    "workingTime": 1670217957458,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "NONE",
    "preventedMatchId": 1,
    "preventedQuantity": "1.300000"
}
```

Maker order 3
```json
{
    "symbol": "BTCUSDT",
    "orderId": 4,
    "orderListId": -1,
    "clientOrderId": "8QZ3taGcU4gND59TxHAcR0",
    "price": "1.000000",
    "origQty": "8.100000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "EXPIRED_IN_MATCH",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1670217957478,
    "updateTime": 1670217957498,
    "isWorking": true,
    "workingTime": 1670217957478,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "NONE",
    "preventedMatchId": 2,
    "preventedQuantity": "8.100000"
}
```

Taker order response

```json
{
    "symbol": "BTCUSDT",
    "orderId": 5,
    "orderListId": -1,
    "clientOrderId": "WRzbhp257NhZsIJW4y2Nri",
    "transactTime": 1670217957498,
    "price": "1.000000",
    "origQty": "3.000000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "SELL",
    "workingTime": 1670217957498,
    "fills": [],
    "preventedMatches": [
        {
            "preventedMatchId": 0,
            "makerOrderId": 2,
            "price": "1.200000",
            "makerPreventedQuantity": "1.200000"
        },
        {
            "preventedMatchId": 1,
            "makerOrderId": 3,
            "price": "1.100000",
            "makerPreventedQuantity": "1.300000"
        },
        {
            "preventedMatchId": 2,
            "makerOrderId": 4,
            "price": "1.000000",
            "makerPreventedQuantity": "8.100000"
        }
    ],
    "selfTradePreventionMode": "EXPIRE_MAKER"
}
```


**Case C - User sends an order with `EXPIRE_TAKER` that would match with existing orders on the book.**

```
Maker order 1: symbol=BTCUSDT side=BUY type=LIMIT quantity=1.2 price=1.2  selfTradePreventionMode=NONE
Maker order 2: symbol=BTCUSDT side=BUY type=LIMIT quantity=1.3 price=1.1  selfTradePreventionMode=NONE
Maker order 3: symbol=BTCUSDT side=BUY type=LIMIT quantity=8.1 price=1    selfTradePreventionMode=NONE
Taker order 1: symbol=BTCUSDT side=SELL type=LIMIT quantity=3 price=1 selfTradePreventionMode=EXPIRE_TAKER
```
**Result:** The orders already on the book will remain, while the taker order will expire.

Maker order 1
```json
{
    "symbol": "BTCUSDT",
    "orderId": 2,
    "orderListId": -1,
    "clientOrderId": "NpwW2t0L4AGQnCDeNjHIga",
    "price": "1.200000",
    "origQty": "1.200000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1670219811986,
    "updateTime": 1670219811986,
    "isWorking": true,
    "workingTime": 1670219811986,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "NONE"
}
```

Maker order 2

```json
{
    "symbol": "BTCUSDT",
    "orderId": 3,
    "orderListId": -1,
    "clientOrderId": "TSAmJqGWk4YTB2yA9p04UO",
    "price": "1.100000",
    "origQty": "1.300000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1670219812007,
    "updateTime": 1670219812007,
    "isWorking": true,
    "workingTime": 1670219812007,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "NONE"
}
```

Maker order 3
```json
{
    "symbol": "BTCUSDT",
    "orderId": 4,
    "orderListId": -1,
    "clientOrderId": "L6FmpCJJP6q4hCNv4MuZDG",
    "price": "1.000000",
    "origQty": "8.100000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1670219812026,
    "updateTime": 1670219812026,
    "isWorking": true,
    "workingTime": 1670219812026,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "NONE"
}
```

Taker order status

```json
{
    "symbol": "BTCUSDT",
    "orderId": 5,
    "orderListId": -1,
    "clientOrderId": "kocvDAi4GNN2y1l1Ojg1Ri",
    "price": "1.000000",
    "origQty": "3.000000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "EXPIRED_IN_MATCH",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "SELL",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1670219812046,
    "updateTime": 1670219812046,
    "isWorking": true,
    "workingTime": 1670219812046,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "EXPIRE_TAKER",
    "preventedMatchId": 0,
    "preventedQuantity": "3.000000"
}
```


**Case D - User sends an order with `EXPIRE_BOTH` that would match with an existing order on the book.**

```
Maker order: symbol=BTCUSDT side=BUY type=LIMIT quantity=1 price=1 selfTradePreventionMode=NONE
Taker order: symbol=BTCUSDT side=SELL type=LIMIT quantity=3 price=1 selfTradePreventionMode=EXPIRE_BOTH
```

**Result:** Both orders will expire.

Maker order

```json
{
    "symbol": "BTCUSDT",
    "orderId": 2,
    "orderListId": -1,
    "clientOrderId": "2JPC8xjpLq6Q0665uYWAcs",
    "price": "1.000000",
    "origQty": "1.000000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "EXPIRED_IN_MATCH",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1673842412831,
    "updateTime": 1673842413170,
    "isWorking": true,
    "workingTime": 1673842412831,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "NONE",
    "preventedMatchId": 0,
    "preventedQuantity": "1.000000"
}
```

Taker order

```json
{
    "symbol": "BTCUSDT",
    "orderId": 5,
    "orderListId": -1,
    "clientOrderId": "qMaz8yrOXk2iUIz74cFkiZ",
    "transactTime": 1673842413170,
    "price": "1.000000",
    "origQty": "3.000000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "EXPIRED_IN_MATCH",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "SELL",
    "workingTime": 1673842413170,
    "fills": [],
    "preventedMatches": [
        {
            "preventedMatchId": 0,
            "makerOrderId": 2,
            "price": "1.000000",
            "takerPreventedQuantity": "3.000000",
            "makerPreventedQuantity": "1.000000"
        }
    ],
    "selfTradePreventionMode": "EXPIRE_BOTH",
    "tradeGroupId": 1,
    "preventedQuantity": "3.000000"
}
```

**Case E - User has an order on the book with `EXPIRE_MAKER`, then sends a new order with `EXPIRE_TAKER` that would match the order on the book.**


```
Maker order: symbol=BTCUSDT side=BUY type=LIMIT quantity=1 price=1 selfTradePreventionMode=EXPIRE_MAKER
Taker order: symbol=BTCUSDT side=SELL type=LIMIT quantity=1 price=1 selfTradePreventionMode=EXPIRE_TAKER
```

**Result:** The taker order's STP mode will be used, so the taker order will expire.

Maker order
```json
{
    "symbol": "BTCUSDT",
    "orderId": 0,
    "orderListId": -1,
    "clientOrderId": "jFUap8iFwwgqIpOfAL60GS",
    "price": "1.000000",
    "origQty": "1.000000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1670220769261,
    "updateTime": 1670220769261,
    "isWorking": true,
    "workingTime": 1670220769261,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "EXPIRE_MAKER"
}
```

Taker order
```json
{
    "symbol": "BTCUSDT",
    "orderId": 1,
    "orderListId": -1,
    "clientOrderId": "zxrvnNNm1RXC3rkPLUPrc1",
    "transactTime": 1670220800315,
    "price": "1.000000",
    "origQty": "1.000000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "EXPIRED_IN_MATCH",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "SELL",
    "workingTime": 1670220800315,
    "fills": [],
    "preventedMatches": [
        {
            "preventedMatchId": 0,
            "makerOrderId": 0,
            "price": "1.000000",
            "takerPreventedQuantity": "1.000000"
        }
    ],
    "selfTradePreventionMode": "EXPIRE_TAKER",
    "preventedQuantity": "1.000000"
}
```


**Case F - User sends a MARKET order with `EXPIRE_MAKER` that would match with existing orders on the book.**


```
Maker order: symbol=BTCUSDT side=BUY type=LIMIT quantity=1 price=1  selfTradePreventionMode=NONE
Taker order: symbol=BTCUSDT side=SELL type=MARKET quantity=1 selfTradePreventionMode=EXPIRE_MAKER
```

**Result:** Due to STP, the order on the book expires with status `EXPIRED_IN_MATCH`.
Due to low liquidity on the book, the new order also expired with status `EXPIRED`.

Maker order

```json
{
    "symbol": "BTCUSDT",
    "orderId": 2,
    "orderListId": -1,
    "clientOrderId": "7sgrQQInL69XDMQpiqMaG2",
    "price": "1.000000",
    "origQty": "1.000000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "EXPIRED_IN_MATCH",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.000000",
    "icebergQty": "0.000000",
    "time": 1670222557456,
    "updateTime": 1670222557478,
    "isWorking": true,
    "workingTime": 1670222557456,
    "origQuoteOrderQty": "0.000000",
    "selfTradePreventionMode": "NONE",
    "preventedMatchId": 0,
    "preventedQuantity": "1.000000"
}
```

Taker order

```json
{
    "symbol": "BTCUSDT",
    "orderId": 3,
    "orderListId": -1,
    "clientOrderId": "zqhsgGDEcdhxy2oza2Ljxd",
    "transactTime": 1670222557478,
    "price": "0.000000",
    "origQty": "1.000000",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "EXPIRED",
    "timeInForce": "GTC",
    "type": "MARKET",
    "side": "SELL",
    "workingTime": 1670222557478,
    "fills": [],
    "preventedMatches": [
        {
            "preventedMatchId": 0,
            "makerOrderId": 2,
            "price": "1.000000",
            "makerPreventedQuantity": "1.000000"
        }
    ],
    "selfTradePreventionMode": "EXPIRE_MAKER"
}
```

**Case G - User sends a limit order with `DECREMENT` that would match with an existing order on the book.**

```
Maker order： symbol=BTCUSDT side=BUY  type=LIMIT quantity=6 price=2  selfTradePreventionMode=NONE
Taker order： symbol=BTCUSDT side=SELL type=LIMIT quantity=2 price=2  selfTradePreventionMode=DECREMENT
```

**Result:** Both orders have a preventedQuantity of 2. Since this is all of the taker order's available quantity, this order expires due to STP.

Maker order

```json
{
    "symbol": "BTCUSDT",
    "orderId": 23,
    "orderListId": -1,
    "clientOrderId": "Kxb4RpsBhfQrkK2r2YO2Z9",
    "price": "2.00000000",
    "origQty": "6.00000000",
    "executedQty": "0.00000000",
    "cummulativeQuoteQty": "0.00000000",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.00000000",
    "icebergQty": "0.00000000",
    "time": 1741682807892,
    "updateTime": 1741682816376,
    "isWorking": true,
    "workingTime": 1741682807892,
    "origQuoteOrderQty": "0.00000000",
    "selfTradePreventionMode": "DECREMENT",
    "preventedMatchId": 4,
    "preventedQuantity": "2.00000000"
}
```

Taker order

```json
{
    "symbol": "BTCUSDT",
    "orderId": 24,
    "orderListId": -1,
    "clientOrderId": "dwf3qOzD7GM9ysDn9XG9AS",
    "price": "2.00000000",
    "origQty": "2.00000000",
    "executedQty": "0.00000000",
    "cummulativeQuoteQty": "0.00000000",
    "status": "EXPIRED_IN_MATCH",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "SELL",
    "stopPrice": "0.00000000",
    "icebergQty": "0.00000000",
    "time": 1741682816376,
    "updateTime": 1741682816376,
    "isWorking": true,
    "workingTime": 1741682816376,
    "origQuoteOrderQty": "0.00000000",
    "selfTradePreventionMode": "DECREMENT",
    "preventedMatchId": 4,
    "preventedQuantity": "2.00000000"
}
```

**Case H - User sends a limit order with `TRANSFER` that would match with an existing order under the same `tradeGroupId`.**

Balances before placing orders

Maker balance

```json
{
    "balances": [
        {
            "asset": "BTC",
            "free": "20000.00000000",
            "locked": "0.00000000"
        },
        {
            "asset": "USDT",
            "free": "20000.00000000",
            "locked": "0.00000000"
        }
    ]
}
```

Taker balance

```json
{
    "balances": [
        {
            "asset": "BTC",
            "free": "20000.00000000",
            "locked": "0.00000000"
        },
        {
            "asset": "USDT",
            "free": "20000.00000000",
            "locked": "0.00000000"
        }
    ]
}
```

```
Maker Order: symbol=BTCUSDT side=BUY  type=LIMIT quantity=0.6 price=0.2  selfTradePreventionMode=TRANSFER tradeGroupId=1
Taker Order: symbol=BTCUSDT side=SELL type=LIMIT quantity=0.2 price=0.2  selfTradePreventionMode=TRANSFER tradeGroupId=1
```

**Result:** Both orders have a preventedQuantity of 0.2. Since this is all of the taker order's quantity, the order expires due to STP.

Maker order

```json
{
    "symbol": "BTCUSDT",
    "orderId": 12,
    "orderListId": -1,
    "clientOrderId": "zEyu9HGqiT5YUaXXhKr1MR",
    "price": "0.20000000",
    "origQty": "0.60000000",
    "executedQty": "0.00000000",
    "cummulativeQuoteQty": "0.00000000",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.00000000",
    "icebergQty": "0.00000000",
    "time": 1762852466582,
    "updateTime": 1762852522145,
    "isWorking": true,
    "workingTime": 1762852466582,
    "origQuoteOrderQty": "0.00000000",
    "selfTradePreventionMode": "TRANSFER",
    "preventedMatchId": 3,
    "preventedQuantity": "0.20000000"
}
```

Taker order

```json
{
    "symbol": "BTCUSDT",
    "orderId": 13,
    "orderListId": -1,
    "clientOrderId": "6T06cph3Et2yFNnGpHdejh",
    "transactTime": 1762852522145,
    "price": "0.20000000",
    "origQty": "0.20000000",
    "executedQty": "0.00000000",
    "origQuoteOrderQty": "0.00000000",
    "cummulativeQuoteQty": "0.00000000",
    "status": "EXPIRED_IN_MATCH",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "SELL",
    "workingTime": 1762852522145,
    "fills": [],
    "preventedMatches": [
        {
            "preventedMatchId": 3,
            "makerSymbol": "BTCUSDT",
            "makerOrderId": 12,
            "price": "0.20000000",
            "takerPreventedQuantity": "0.20000000",
            "makerPreventedQuantity": "0.20000000"
        }
    ],
    "selfTradePreventionMode": "TRANSFER",
    "tradeGroupId": 1,
    "preventedQuantity": "0.20000000"
}
```

Balances after STP is triggered

Maker balance

```json
{
    "balances": [
        {
            "asset": "BTC",
            "free": "20000.20000000",
            "locked": "0.00000000"
        },
        {
            "asset": "USDT",
            "free": "19999.88000000",
            "locked": "0.08000000"
        }
    ]
}
```

Taker balance

```json
{
    "balances": [
        {
            "asset": "BTC",
            "free": "19999.80000000",
            "locked": "0.00000000"
        },
        {
            "asset": "USDT",
            "free": "20000.04000000",
            "locked": "0.00000000"
        }
    ]
}
```
