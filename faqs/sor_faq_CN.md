# Smart Order Routing (SOR)

**Disclaimer:**

* The symbols and values used here are fictional and do not represent settings in a real exchange.
* For simplicity, the examples in this document do not include commissions.

### What is Smart Order Routing (SOR)?

**Smart Order Routing** (SOR) allows clients to potentially access better liquidity by using liquidity from other order books that share the same base asset (`base asset`) and interchangeable quote assets (`interchangeable quote assets`). Interchangeable quote assets are quote assets with a fixed 1:1 exchange rate, such as stablecoins pegged to the same fiat currency.

Note that although the quote assets are interchangeable, when selling the base asset, you will always receive the quote asset corresponding to the symbol of the order you submitted.

When you place an order using `SOR`, it looks for the best price level across the order books configured for SOR and trades from them where possible.

**Note:** If a SOR order cannot be fully filled based on the eligible order book liquidity, an IOC limit order (`LIMIT IOC`) or market order (`MARKET`) will expire immediately, while a GTC limit order (`LIMIT GTC`) will place the remaining quantity on the order book to which you originally submitted the order.

**Example 1**

Consider an SOR configuration containing trading pairs `BTCUSDT`, `BTCUSDC`, and `BTCUSDP`, with the following order books on the ask side for these symbols:

```
BTCUSDT quantity 3 price 30,800
BTCUSDT quantity 3 price 30,500

BTCUSDC quantity 1 price 30,000
BTCUSDC quantity 1 price 28,000

BTCUSDP quantity 1 price 35,000
BTCUSDP quantity 1 price 29,000
```

If you place a limit GTC buy order for `BTCUSDT` with a price of 31,000 and quantity of 0.5 without SOR, and the best ask price in the `BTCUSDT` order book is 30,500 USDT, you will spend 15,250 USDT and receive 0.5 BTC.

If you place a GTC limit buy order (`LIMIT GTC BUY`) for `BTCUSDT` with a quantity of 0.5 and price of 31,000 using SOR, your order will be matched against the best ask price across all SOR-covered trading pairs, which is `BTCUSDC` at a price of 28,000. You will spend 14,000 USDT (not USDC!), and receive 0.5 BTC.

```javascript
{
    "symbol": "BTCUSDT",
    "orderId": 2,
    "orderListId": -1,
    "clientOrderId": "sBI1KM6nNtOfj5tccZSKly",
    "transactTime": 1689149087774,
    "price": "31000.00000000",
    "origQty": "0.50000000",
    "executedQty": "0.50000000",
    "cummulativeQuoteQty": "14000.00000000",
    "status": "FILLED",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "workingTime": 1689149087774,
    "fills": [
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "28000.00000000",
            "qty": "0.50000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 0
        }
    ],
    "workingFloor": "SOR",
    "selfTradePreventionMode": "NONE",
    "usedSor": true
}
```

**Example 2**

Using the same order books as in Example 1:

```
BTCUSDT quantity 3 price 30,800
BTCUSDT quantity 3 price 30,500

BTCUSDC quantity 1 price 30,000
BTCUSDC quantity 1 price 28,000

BTCUSDP quantity 1 price 35,000
BTCUSDP quantity 1 price 29,000
```

If you place a GTC limit buy order (`LIMIT GTC BUY`) for `BTCUSDT` with a quantity of 5 and price of 31,000 without SOR:

* Fills 3 `BTCUSDT` at 30,500 USDT from the `BTCUSDT` order book, purchasing 3 BTC for 91,500 USDT.
* Then fills 3 `BTCUSDT` at 30,800 USDT from the `BTCUSDT` order book, purchasing 2 BTC for 61,600 USDT.

In total you spend 153,100 USDT and receive 5 BTC.

If you place the same GTC limit buy order (`LIMIT GTC BUY`) for `BTCUSDT` with a quantity of 5 and price of 31,000 using SOR:

* Fills 1 BTCUSDC at 28,000 from the `BTCUSDC` order book, purchasing 1 BTC for 28,000 USDT.
* Fills 1 BTCUSDP at 29,000 from the `BTCUSDP` order book, purchasing 1 BTC for 29,000 USDT.
* Fills 1 BTCUSDC at 30,000 from the `BTCUSDC` order book, purchasing 1 BTC for 30,000 USDT.
* Fills 3 BTCUSDT at 30,500 from the `BTCUSDT` order book, purchasing 2 BTC for 61,000 USDT.

In total you spend 148,000 USDT and receive 5 BTC.

```javascript
{
    "symbol": "BTCUSDT",
    "orderId": 2,
    "orderListId": -1,
    "clientOrderId": "tHonoNjWfOSaKiTygN3bfY",
    "transactTime": 1689146154686,
    "price": "31000.00000000",
    "origQty": "5.00000000",
    "executedQty": "5.00000000",
    "cummulativeQuoteQty": "148000.00000000",
    "status": "FILLED",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "workingTime": 1689146154686,
    "fills": [
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "28000.00000000",
            "qty": "1.00000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 0
        },
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "29000.00000000",
            "qty": "1.00000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 1
        },
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "30000.00000000",
            "qty": "1.00000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 2
        },
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "30500.00000000",
            "qty": "2.00000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 3
        }
    ],
    "workingFloor": "SOR",
    "selfTradePreventionMode": "NONE",
    "usedSor": true
}
```

**Example 3**

Using the same order books as in Examples 1 and 2:

```
BTCUSDT quantity 3 price 30,800
BTCUSDT quantity 3 price 30,500

BTCUSDC quantity 1 price 30,000
BTCUSDC quantity 1 price 28,000

BTCUSDP quantity 1 price 35,000
BTCUSDP quantity 1 price 29,000
```

If you place a market buy order (`MARKET` `BUY`) for `BTCUSDT` with a quantity of 11 using SOR, but there are only a total of 10 BTC available across all eligible order books, the remaining quantity of 1 will expire once all order books in the SOR configuration are exhausted.

```javascript
{
    "symbol": "BTCUSDT",
    "orderId": 2,
    "orderListId": -1,
    "clientOrderId": "jdFYWTNyzplbNvVJEzQa0o",
    "transactTime": 1689149513461,
    "price": "0.00000000",
    "origQty": "11.00000000",
    "executedQty": "10.00000000",
    "cummulativeQuoteQty": "305900.00000000",
    "status": "EXPIRED",
    "timeInForce": "GTC",
    "type": "MARKET",
    "side": "BUY",
    "workingTime": 1689149513461,
    "fills": [
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "28000.00000000",
            "qty": "1.00000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 0
        },
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "29000.00000000",
            "qty": "1.00000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 1
        },
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "30000.00000000",
            "qty": "1.00000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 2
        },
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "30500.00000000",
            "qty": "3.00000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 3
        },
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "30800.00000000",
            "qty": "3.00000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 4
        },
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "35000.00000000",
            "qty": "1.00000000",
            "commission": "0.00000000",
            "commissionAsset": "BTC",
            "tradeId": -1,
            "allocId": 5
        }
    ],
    "workingFloor": "SOR",
    "selfTradePreventionMode": "NONE",
    "usedSor": true
}
```

**Example 4**

Assume an SOR configuration with trading pairs `BTCUSDT`, `BTCUSDC`, and `BTCUSDP`, and the following bid-side order books for these pairs:

```
BTCUSDT quantity 5 price 29,500

BTCUSDC quantity 5 price 35,000
BTCUSDC quantity 5 price 30,000

BTCUSDP quantity 5 price 28,000
```

If you place a GTC limit sell order (`LIMIT GTC SELL`) on `BTCUSDT` at a price of 29,000 to sell 10 BTC without SOR, you will sell 5 BTC and receive 147,500 USDT. Since there are no better prices available on the `BTCUSDT` order book, the remaining (unfilled) quantity will be left on the order book at a price of 29,000.

If you place a GTC limit sell order (`LIMIT GTC SELL`) for `BTCUSDT` using SOR:

* Fills 5 BTCUSDC at 35,000 from the `BTCUSDC` order book, selling 5 BTC for 175,000 USDT.
* Fills 5 BTCUSDC at 30,000 from the `BTCUSDC` order book, selling 5 BTC for 150,000 USDT.

In total you sell 10 BTC and receive 325,000 USDT.

```javascript
{
    "symbol": "BTCUSDT",
    "orderId": 1,
    "orderListId": -1,
    "clientOrderId": "W1iXSng1fS77dvanQJDGA5",
    "transactTime": 1689147920113,
    "price": "29000.00000000",
    "origQty": "10.00000000",
    "executedQty": "10.00000000",
    "cummulativeQuoteQty": "325000.00000000",
    "status": "FILLED",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "SELL",
    "workingTime": 1689147920113,
    "fills": [
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "35000.00000000",
            "qty": "5.00000000",
            "commission": "0.00000000",
            "commissionAsset": "USDT",
            "tradeId": -1,
            "allocId": 0
        },
        {
            "matchType": "ONE_PARTY_TRADE_REPORT",
            "price": "30000.00000000",
            "qty": "5.00000000",
            "commission": "0.00000000",
            "commissionAsset": "USDT",
            "tradeId": -1,
            "allocId": 1
        }
    ],
    "workingFloor": "SOR",
    "selfTradePreventionMode": "NONE",
    "usedSor": true
}
```

**Summary: The goal of SOR is to potentially achieve better liquidity across order books with interchangeable quote assets (`interchangeable quote assets`). Better liquidity can allow orders to be filled at better prices and more completely.**

### Which trading pairs support SOR?

The current SOR configuration can be queried via the exchange info endpoint (REST `GET /api/v3/exchangeInfo`, WebSocket API `exchangeInfo`).

```json
{
    "sors": [
        {
            "baseAsset": "BTC",
            "symbols": ["BTCUSDT", "BTCUSDC", "BTCUSDP"]
        }
    ]
}
```

The `sors` field is optional.
If SOR is not available, this field will be omitted from the response.

### How do I place a SOR order?

Via the REST API endpoint `POST /api/v3/sor/order`.

Via the WebSocket API endpoint `sor.order.place`.

### In the API response, what does the `workingFloor` field mean?

This is a term used to identify the last update operation of the order (fill, expiry, placement as a new order, etc.).

If `workingFloor` is `SOR`, it means your order interacted with other eligible order books in the SOR configuration.

If `workingFloor` is `EXCHANGE`, it means your order interacted with the order book to which you submitted the order.

### How do I check if an order used SOR?

You can query it the same way you query any other order. The main difference is that for orders that used SOR, the response will contain two additional fields: `usedSor` and `workingFloor`.

### What is an allocation?

An **allocation** is a transfer of assets from the exchange to your account. For example, when SOR sources liquidity from an eligible order book, your order will be filled via an allocation. In this case, you do not trade directly; instead SOR trades on your behalf, and you receive an allocation corresponding to the trade SOR made for you.

```javascript
[
    {
        "symbol": "BTCUSDT",           // Symbol the order was submitted to
        "allocationId": 0,
        "allocationType": "SOR",
        "orderId": 2,
        "orderListId": -1,
        "price": "30000.00000000",     // Price of the fill
        "qty": "5.00000000",           // Quantity of the fill
        "quoteQty": "150000.00000000",
        "commission": "0.00000000",
        "commissionAsset": "BTC",
        "time": 1688379272280,         // Time the allocation occurred
        "isBuyer": true,
        "isMaker": false,
        "isAllocator": false
    }
]
```

### How do I get fill details for orders that used SOR?

When a SOR order trades with an order book other than the one the order was submitted to, the order is filled via an allocation rather than a trade. Orders placed using SOR may have both allocations and trades.

In the API response, you can view the `fills` field. Allocations have an `allocId` and `matchType`: "ONE_PARTY_TRADE_REPORT", while trades will have a non-negative `tradeId`.

You can query allocations and trades using the following:

Query allocations: REST API `GET /api/v3/myAllocations` or WebSocket API `myAllocations`.

Query trades: REST API `GET /api/v3/myTrades` or WebSocket API `myTrades`.
