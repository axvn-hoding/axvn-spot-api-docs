# Pegged Orders

**Disclaimer:**

* This glossary only applies to the Spot (`SPOT`) exchange.
* The trading pairs and prices used here are fictional and do not reflect actual exchange settings.
* For simplicity, the examples in this document do not include commissions.

## What are pegged orders?

Pegged orders are essentially **limit orders** whose price is derived from the order book.

For example, instead of using a specific price (e.g., sell 1 BTC for at least 100,000 USDC), you can send an order like “sell 1 BTC at the best ask price” to queue your order behind the highest-priced orders in the order book. Or, with an order like “buy 1 BTC at 100,000 USDT or the best bid, fill immediately or cancel”, you can target sellers at the lowest price (and only at that price).

Pegged orders provide market makers with a way to match the best price with minimal latency, while retail traders can fill quickly at the best price with minimal slippage.

Pegged orders are also known as “best bid-offer” (`best bid-offer`) or BBO orders.

## How do I place a pegged order?

Please refer to the following table:

<table border="1" cellpadding="5" cellspacing="0">
  <thead>
    <tr>
      <th>API</th>
      <th>Request</th>
      <th>Parameters</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3">REST API</td>
      <td><code>POST /api/v3/order</code></td>
      <td rowspan="6">
        <p><code>pegPriceType</code>:</p>
        <ul>
          <li><code>PRIMARY</code> — Best price on the same side of the order book</li>
          <li><code>MARKET</code> — Best price on the opposite side of the order book</li>
        </ul>
        <p>
        <code>pegOffsetType</code> and <code>pegOffsetValue PRICE_LEVEL</code> — Offset existing price levels to go deeper into the order book</p>
        <p>For order lists: (For details, please refer to the API documentation.)</p>
        <ul>
          <li>OCO uses <code>above*</code> and <code>below*</code> prefixes.</li>
          <li>OTO uses <code>working*</code> and <code>pending*</code> prefixes.</li>
          <li>OTOCO uses <code>working*</code>, <code>pendingAbove*</code>, and <code>pendingBelow*</code> prefixes.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>
        <code>POST /api/v3/orderList/*</code><br>
      </td>
    </tr>
    <tr>
      <td><code>POST /api/v3/cancelReplace</code></td>
    </tr>
    <tr>
      <td rowspan="3">WebSocket API</td>
      <td><code>order.place</code></td>
    </tr>
    <tr>
      <td>
        <code>orderList.place.*</code><br>
      </td>
    </tr>
    <tr>
      <td><code>order.cancelReplace</code></td>
    </tr>
    <tr>
      <td rowspan="3">FIX API</td>
      <td>NewOrderSingle <code>&lt;D&gt;</code></td>
      <td rowspan="3"><code>OrdType=PEGGED</code>, <code>&lt;PegInstructions&gt;</code> component block, <code>PeggedPrice</code> field.</td>
    </tr>
    <tr>
      <td>NewOrderList <code>&lt;E&gt;</code></td>
    </tr>
    <tr>
      <td>OrderCancelRequestAndNewOrderSingle <code>&lt;XCN&gt;</code></td>
    </tr>
  </tbody>
</table>

Currently, [Smart Order Routing (SOR)](sor_faq_CN.md) does not support pegged orders.

This example REST API response shows that for a pegged order, `peggedPrice` reflects the selected price, while `price` is the original order price (set to zero if not specified).

```json
{
    "symbol": "BTCUSDT",
    "orderId": 18,
    "orderListId": -1,
    "clientOrderId": "q1fKs4Y7wgE61WSFMYRFKo",
    "transactTime": 1750313780050,
    "price": "0.00000000",
    "pegPriceType": "PRIMARY_PEG",
    "peggedPrice": "0.04000000",
    "origQty": "1.00000000",
    "executedQty": "0.00000000",
    "origQuoteOrderQty": "0.00000000",
    "cummulativeQuoteQty": "0.00000000",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "workingTime": 1750313780050,
    "fills": [],
    "selfTradePreventionMode": "NONE"
}
```

## Which order types support pegged orders?

This feature supports all order types except `MARKET` orders.

Since `STOP_LOSS` and `TAKE_PROFIT` orders both place a `MARKET` order when the stop condition is met, these order types cannot use peg instructions.

### Limit Orders

Pegged limit orders enter the market immediately at the current best price:

* `LIMIT`
  * When using `pegPriceType=PRIMARY_PEG`, only `timeInForce=GTC` is allowed.
* `LIMIT_MAKER`
  * Only `pegPriceType=PRIMARY_PEG` is allowed.

### Stop-Limit Orders

When a price movement triggers a stop order (via stop price or trailing stop), pegged stop-limit orders enter the market at the best price:

* `STOP_LOSS_LIMIT`
* `TAKE_PROFIT_LIMIT`

This means the stop order will use the best price at the time of triggering, which will differ from the price when the stop order was placed. These orders can only peg to limit price, not to stop price.

### OCO

OCO order lists can use peg instructions.

* Any order in an OCO can use pegging: both the above order and the below order, or only one of them.
* Pegged orders will fill at the best price when placed in the order book:
  * `LIMIT_MAKER` orders enter the market immediately at the current best price
  * `STOP_LOSS_LIMIT` and `TAKE_PROFIT_LIMIT` will enter the market at the best price when triggered
* `STOP_LOSS` and `TAKE_PROFIT` orders cannot use peg instructions.

### OTO and OTOCO

OTO order lists can also use peg instructions.

* Any order in an OTO can use pegging: both the working order and the pending order, or only one of them.
* Pegged working orders enter the market immediately at the current best price.
* When the working order is complete, the pegged pending limit order enters the market at the best price.
* Pegged pending stop-limit orders enter the market at the best price when triggered.

OTOCO order lists can also use pegged orders, in a manner similar to OTO and OCO order lists.

## Which trading pairs support pegged orders?

Please refer to the exchange information request and look for the `pegInstructionsAllowed` field. If this field is set to true, pegged orders can be used on this trading pair.

## Which filters apply to pegged orders?

Pegged orders must pass all applicable filters for the selected price:

* `PRICE_FILTER`
* `PERCENT_PRICE` and `PERCENT_PRICE_BY_SIDE`
* `NOTIONAL` and `MIN_NOTIONAL` (consider `quantity`)

If a pegged order specifies a `price`, it must pass validation on both `price` and `peggedPrice`.

Conditional orders and pegged pending orders in OTO order lists will be (re-)validated when triggered, and may be rejected afterward.
