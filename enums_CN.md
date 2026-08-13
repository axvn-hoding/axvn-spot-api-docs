# Enum Definitions

This applies to both REST API and WebSocket API.

## Trading Pair Status (status)

* `TRADING` - Currently trading
* `END_OF_DAY` - End of day
* `HALT` - Trading halted (the trading pair has been delisted)
* `BREAK` - Trading paused
* `CANCEL_ONLY` - Cancel orders only

<a id="account-and-symbol-permissions"></a>

## Account and Symbol Permissions (permissions)

* `SPOT` - Spot trading
* `MARGIN` - Margin trading
* `LEVERAGED` - Leveraged tokens
* `TRD_GRP_002` - Trading Group 002
* `TRD_GRP_003` - Trading Group 003
* `TRD_GRP_004` - Trading Group 004
* `TRD_GRP_005` - Trading Group 005
* `TRD_GRP_006` - Trading Group 006
* `TRD_GRP_007` - Trading Group 007
* `TRD_GRP_008` - Trading Group 008
* `TRD_GRP_009` - Trading Group 009
* `TRD_GRP_010` - Trading Group 010
* `TRD_GRP_011` - Trading Group 011
* `TRD_GRP_012` - Trading Group 012
* `TRD_GRP_013` - Trading Group 013
* `TRD_GRP_014` - Trading Group 014
* `TRD_GRP_015` - Trading Group 015
* `TRD_GRP_016` - Trading Group 016
* `TRD_GRP_017` - Trading Group 017
* `TRD_GRP_018` - Trading Group 018
* `TRD_GRP_019` - Trading Group 019
* `TRD_GRP_020` - Trading Group 020
* `TRD_GRP_021` - Trading Group 021
* `TRD_GRP_022` - Trading Group 022
* `TRD_GRP_023` - Trading Group 023
* `TRD_GRP_024` - Trading Group 024
* `TRD_GRP_025` - Trading Group 025

## Order Status (status)

Status | Description
-----------|--------------
`NEW` | The order has been accepted by the trading engine.
`PENDING_NEW` | The order is in a pending (`PENDING`) state until the `working order` in its order list is fully filled.
`PARTIALLY_FILLED` | Part of the order has been filled.
`FILLED`| The order has been completely filled.
`CANCELED` | The order was cancelled by the user.
`PENDING_CANCEL` | Cancellation in progress (currently not used)
`REJECTED`       | The order was not accepted by the trading engine and was not processed.
`EXPIRED` | The order was cancelled according to the order type's rules (e.g., a LIMIT FOK order with no fill, LIMIT IOC or partially filled MARKET order)<br/>or was cancelled by the trading engine (e.g., orders cancelled during liquidation, orders cancelled during exchange maintenance)
`EXPIRED_IN_MATCH` | Indicates the order expired due to STP. (e.g., an order with `EXPIRE_TAKER` matched with an existing order on the book belonging to the same account or same `tradeGroupId`)

## Order List Status (listStatusType)

Status | Description
-----------|--------------
`RESPONSE` | Used when the ListStatus is responding to a failed action. (e.g., placing an order list or canceling an order list)
`EXEC_STARTED` | The order list has been placed or there has been an update to the order list status.
`UPDATED`  | The clientOrderId of an order in the order list has been changed.
`ALL_DONE` | The order list has finished executing and is thus no longer active.

## Order Status in an Order List (listOrderStatus)

Status | Description
-----------|--------------
`EXECUTING` | The order list has been placed or there has been an update to the order list status.
`ALL_DONE`| The order list has finished executing and is thus no longer active.
`REJECT` | Used when the ListStatus is responding to a failed action during order placement or order list cancellation.

## Order List Types

* `OCO`
* `OTO`

<a id="allocationtype"></a>

## Allocation Type

* `SOR`

<a id="ordertypes"></a>

## Order Types (orderTypes, type)

* `LIMIT` - Limit order
* `MARKET` - Market order
* `STOP_LOSS` - Stop loss order
* `STOP_LOSS_LIMIT` - Stop loss limit order
* `TAKE_PROFIT` - Take profit order
* `TAKE_PROFIT_LIMIT` - Take profit limit order
* `LIMIT_MAKER` - Limit maker order

<a id="orderresponsetype"></a>

## Order Response Type (newOrderRespType)

* `ACK`
* `RESULT`
* `FULL`

## Working Floor

* `EXCHANGE` - Regular trading
* `SOR` - Smart Order Routing

<a id="side"></a>

## Order Side (side)

* `BUY` - Buy
* `SELL` - Sell

<a id="timeinforce"></a>

## Time In Force (timeInForce)

This defines how long an order will be active before expiration.

Status | Description
-----------|--------------
`GTC` | Good Till Canceled <br/> The order will remain active until it is filled or canceled.
`IOC` | Immediate Or Cancel <br/> The order will try to fill as much as possible before expiring.
`FOK` | Fill Or Kill <br/> The order will expire if it cannot be completely filled.


## Rate Limit Types (rateLimitType)

* REQUEST_WEIGHT - Maximum sum of request weights per unit time

```json
{
    "rateLimitType": "REQUEST_WEIGHT",
    "interval": "MINUTE",
    "intervalNum": 1,
    "limit": 6000
}
```

* ORDERS - Maximum number of orders per unit time

```json
{
    "rateLimitType": "ORDERS",
    "interval": "SECOND",
    "intervalNum": 1,
    "limit": 10
}
```

* RAW_REQUESTS - Maximum number of requests per unit time

```json
{
    "rateLimitType": "RAW_REQUESTS",
    "interval": "MINUTE",
    "intervalNum": 5,
    "limit": 61000
}
```

## Rate Limit Intervals (interval)

* SECOND
* MINUTE
* DAY

<a id="stpmodes"></a>

## STP Modes

Please refer to [Self Trade Prevention (STP) FAQ](faqs/stp_faq_CN.md).

* `NONE`
* `EXPIRE_MAKER`
* `EXPIRE_TAKER`
* `EXPIRE_BOTH`
* `DECREMENT`
* `TRANSFER`

## Possible Execution Types:

Status | Description
-----------|--------------
`NEW`      | A new order has been accepted by the engine.
`CANCELED` | The order was cancelled by the user.
`REPLACED` | The order has been amended.
`REJECTED` | A new order was rejected (e.g. when the new order in a cancel-replace request is rejected but the cancel portion succeeds).
`TRADE`    | Part of the order or all of the order's quantity has filled.
`EXPIRED`  | The order was cancelled according to the Time In Force parameter's rules (e.g. LIMIT FOK order with no fill or partially filled LIMIT IOC order) or by the exchange (e.g. orders cancelled during liquidation or maintenance).
`TRADE_PREVENTION` | The order expired due to STP being triggered.

<a id="execution rules"></a>

## Execution Rules

* `PRICE_RANGE`

<a id="expiryreasons"></a>

## Expiry Reasons

* `NONE`
* `REJECTED`
* `EXCHANGE_CANCELED`
* `OCO_TRIGGER`
* `OTO_PHASE_ONE_EXPIRED`
* `UNFILLED_IOC_QUANTITY_EXPIRED`
* `UNFILLED_FOK_ORDER_EXPIRED`
* `INSUFFICIENT_LIQUIDITY`
* `EXECUTION_RULE_PRICE_RANGE_EXCEEDED`
