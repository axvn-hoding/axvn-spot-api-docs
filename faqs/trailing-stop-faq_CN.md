# Trailing Stop Order FAQ

### What is a trailing stop order?

A trailing stop is a tool used to track market price movements. A new parameter `trailingDelta` has been added to the Spot API to define when an order is triggered, with its value expressed in basis points (BIPS).

Trailing stop orders allow prices to move in a favorable direction while limiting movement in an unfavorable direction.

BUY orders: A _lower_ price is favorable. Continued price _declines_ are allowed. However, if the market price _rises_ by the predefined delta from the lowest point after order placement, the order will be triggered.

SELL orders: A _higher_ price is favorable. Continued price _rises_ are allowed. However, if the market price _falls_ by the predefined delta from the highest point after order placement, the order will be triggered.

### What are basis points (BIPs)?

Basis points, also known as bps or bips, are used in finance to describe percentage changes.

Basis Points | Percentage | Decimal
---- | ---------- | ----------
1 | 0.01% | 0.0001
10 | 0.1% | 0.001
100 | 1% | 0.01
1000 | 10% | 0.1

For example, a `STOP_LOSS` SELL order with `trailingDelta=100` creates a trailing stop sell order. After submission, the order tracks price movements and will be triggered as a market order when the price drops `1%` from the highest price since submission.

### What order types support trailing stop?

`STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, and `TAKE_PROFIT_LIMIT` support the trailing stop feature.

OCO (limit stop) orders support trailing stop in their stop-loss leg. If the trailing stop is triggered, the associated LIMIT order will be cancelled.

### How do I place a trailing stop order?

Trailing stop orders work similarly to `STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, or `TAKE_PROFIT_LIMIT` orders, but with an additional `trailingDelta` parameter. This parameter must be within the range defined by the `TRAILING_DELTA` filter for the trading pair. The specific values can be found via `GET /api/v3/exchangeInfo`.

Unlike regular stop orders, `stopPrice` is optional for trailing stop orders. If `stopPrice` is set, the order will only begin tracking price movements after that `stopPrice` is triggered. If `stopPrice` is omitted, price tracking begins immediately.

### What price movements trigger a trailing stop order?

Order Type | Direction | Stop Price Condition | Price Movement Required to Trigger
------------------- | ---- | -------------------------- | ----------------------------------
`TAKE_PROFIT`       | SELL | Market price >= stop price | *Pullback* from the highest price
`TAKE_PROFIT_LIMIT` | SELL | Market price >= stop price | *Pullback* from the highest price
`STOP_LOSS`         | SELL | Market price <= stop price | *Pullback* from the highest price
`STOP_LOSS_LIMIT`   | SELL | Market price <= stop price | *Pullback* from the highest price
`STOP_LOSS`         | BUY  | Market price >= stop price | *Rise* from the lowest price
`STOP_LOSS_LIMIT`   | BUY  | Market price >= stop price | *Rise* from the lowest price
`TAKE_PROFIT`       | BUY  | Market price <= stop price | *Rise* from the lowest price
`TAKE_PROFIT_LIMIT` | BUY  | Market price <= stop price | *Rise* from the lowest price

### How do I use the `TRAILING_DELTA` filter?

For `STOP_LOSS` BUY, `STOP_LOSS_LIMIT` BUY, `TAKE_PROFIT` SELL, and `TAKE_PROFIT_LIMIT` SELL orders:

* `trailingDelta` >= `minTrailingAboveDelta`
* `trailingDelta` <= `maxTrailingAboveDelta`

For `STOP_LOSS` SELL, `STOP_LOSS_LIMIT` SELL, `TAKE_PROFIT` BUY, and `TAKE_PROFIT_LIMIT` BUY orders:

* `trailingDelta` >= `minTrailingBelowDelta`
* `trailingDelta` <= `maxTrailingBelowDelta`

### Trailing stop order use cases

#### Use Case A — Trailing stop-loss limit BUY

At `12:01:00`, the latest market price is 40,000. A `STOP_LOSS_LIMIT` BUY order enters the exchange with `stopPrice` set to 44,000, `trailingDelta` of 500 (5%), and a `LIMIT` price of 45,000.

Between `12:01:00` and `12:02:00`, a series of market trades causes the latest price to drop to 37,000. The price has dropped 7.5% (750 BIPS), which exceeds the `trailingDelta`. However, since trailing has not yet started, the price movement is ignored.

Between `12:02:00` and `12:03:00`, market trades cause the price to begin rising. When a trade price equals or exceeds the `stopPrice` (44,000), the order immediately begins tracking price movements. The first qualifying trade is treated as the "lowest price," which in this example is 44,000. If the price rises 500 basis points from 44,000, the order will be triggered.
At this point, the market continues trading, pushing the latest price up to 45,000.

Between `12:03:00` and `12:04:00`, market trades push the latest price up to 46,000. The price has risen approximately 454 basis points from the previously marked lowest price (44,000), but has not yet reached the 500 basis points required to trigger the order.

Between `12:04:00` and `12:05:00`, a series of market trades cause the latest price to begin falling to 42,000. This is a decline from the previously marked lowest price; the lowest price is updated to the current price (42,000). If the market rises 500 basis points from here, the order will be triggered.

Between `12:05:00` and `12:05:30`, market trades push the latest price up to 44,100. The latest trade price reaches or exceeds the 500 basis points threshold set by the order (`44,100 = 42,000 * 1.05`). This causes the order to be triggered and placed on the order book at a limit price of 45,000.

<img alt="image" src="https://user-images.githubusercontent.com/17701918/167370103-ab3b4c05-1e13-4a25-b99a-42f9e4d6adc8.png" />

#### Use Case B — Trailing stop-loss limit SELL

At `12:01:00`, the latest market price is 40,000. A `STOP_LOSS_LIMIT` SELL order enters the exchange with `stopPrice` set to 39,000, `trailingDelta` of 1,000 (10%), and a `LIMIT` price of 38,000.

Between `12:01:00` and `12:02:00`, a series of market trades causes the latest price to rise to 41,500.

Between `12:02:00` and `12:03:00`, market trades cause the price to begin falling. When a trade price equals or falls below the `stopPrice` (39,000), the order immediately begins tracking price movements. The first qualifying trade is treated as the "highest price," which in this example is 39,000. If the price drops 1,000 basis points from 39,000, the order will be triggered.

Between `12:03:00` and `12:04:00`, a series of market trades cause the latest price to drop to 37,000. The price has fallen approximately 512 basis points from the previously marked highest price (39,000), but has not yet reached the 1,000 basis points required to trigger the order.

Between `12:04:00` and `12:05:00`, a series of market trades cause the latest price to begin rising to 41,000. This is a rise from the previously marked highest price; the highest price is updated to the current price (41,000). If the market falls 1,000 basis points from here, the order will be triggered.

Between `12:05:00` and `12:05:30`, some market trades cause the latest price to fall to 36,900. The latest trade price reaches or exceeds the 1,000 basis points threshold set by the order (`36,900 = 41,000 * 0.90`). This causes the order to be triggered and placed on the order book at a limit price of 38,000.

<img alt="image" src="https://user-images.githubusercontent.com/17701918/167370383-eb813cc1-d9b8-4a94-896c-a1a29551e09d.png" />

#### Use Case C — Trailing take-profit limit BUY

At `12:01:00`, the latest market price is 40,000. A `TAKE_PROFIT_LIMIT` BUY order enters the exchange with `stopPrice` set to 38,000, `trailingDelta` of 850 (8.5%), and a `LIMIT` price of 38,500.

Between `12:01:00` and `12:02:00`, a series of market trades causes the latest price to rise to 42,000.

Between `12:02:00` and `12:03:00`, market trades cause the price to begin falling. When a trade price equals or falls to the `stopPrice` (38,000), the order immediately begins tracking price movements. The first qualifying trade is treated as the "lowest price," which in this example is 38,000.
Continued market trading causes the latest price to fall to 37,000. The lowest price is now set to 37,000. If the price rises 850 basis points from 37,000, the order will be triggered.

Between `12:03:00` and `12:04:00`, a series of market trades cause the latest price to begin rising to 39,000. The price has risen approximately 540 basis points from the previously marked lowest price (37,000), but has not yet reached the 850 basis points required to trigger the order.

Between `12:04:00` and `12:05:00`, a series of market trades cause the latest price to begin falling to 38,000, which does not go below the previous lowest price, so no change occurs.

Between `12:05:00` and `12:05:30`, some market trades push the latest price up to 40,145. The latest trade price reaches or exceeds the 850 basis points threshold set by the order (`40,145 = 37,000 * 1.085`). This causes the order to be triggered and placed on the order book at a limit price of 38,500.

<img alt="image" src="https://user-images.githubusercontent.com/17701918/167370339-f1b83c76-790b-4108-8c9a-db2d89a4850f.png" />

#### Use Case D — Trailing take-profit limit SELL

At `12:01:00`, the latest market price is 40,000. A `TAKE_PROFIT_LIMIT` SELL order enters the exchange with `stopPrice` set to 42,000, `trailingDelta` of 750 (7.5%), and a `LIMIT` price of 41,000.

Between `12:01:00` and `12:02:00`, a series of market trades causes the latest price to rise to 41,500.

Between `12:02:00` and `12:03:00`, a series of market trades cause the price to begin falling to 39,000.

Between `12:03:00` and `12:04:00`, a series of market trades cause the latest price to begin rising.
When the latest trade price reaches or exceeds the `stopPrice` set for the order, the order immediately begins tracking. The first qualifying trade price is set as the "highest price." In this example, the highest price is 42,000. If the price falls 750 basis points from the highest price, the order will be triggered.

The market price continues to rise, reaching 45,000. The trailing order's "highest price" is also updated to 45,000. If the price falls 750 basis points from here, the order will be triggered.

Between `12:04:00` and `12:05:00`, a series of market trades cause the latest price to begin falling to 44,000. This corresponds to approximately 222 basis points from the previous highest price (45,000), which does not reach the 750 basis points threshold set by the order, so no trigger occurs.

Between `12:05:00` and `12:06:00`, some market trades push the latest price up to 46,500. As the market rises, the previous highest price is updated to the current price (46,500). If the price falls 750 basis points from here, the order will be triggered.

Between `12:06:00` and `12:06:50`, some market trades cause the latest price to fall to 43,012.5. The current price reaches or exceeds the 750 basis points threshold set by the order (`43,012.5 = 46,500 * 0.925`), causing the order to be triggered and placed on the order book at the LIMIT price of 41,000.

<img alt="image" src="https://user-images.githubusercontent.com/17701918/167370298-172b227a-198d-46ee-a385-5cc267dc253b.png" />

#### Use Case E — Trailing stop order without a stop price

At `12:01:00`, the latest market price is 40,000. A `STOP_LOSS_LIMIT` SELL order enters the exchange with `trailingDelta` of 700 (7%) and a `LIMIT` price of 39,000, but with no `stopPrice` set. The order immediately begins tracking price movements upon creation. If the price falls 700 basis points from 40,000, the order will be triggered.

Between `12:01:00` and `12:02:00`, a series of market trades cause the price to rise to 42,000, setting the trailing "highest price" to 42,000. If the price falls 700 basis points from here (42,000), the order will be triggered.

Between `12:02:00` and `12:03:00`, a series of market trades cause the price to fall to 39,500, which is approximately 595 basis points from the previously tracked highest price (42,000). Since this does not reach the 700 basis points threshold, the order is not triggered.

Between `12:03:00` and `12:04:00`, a series of market trades cause the price to rise to 45,500. As the market rises, the tracked highest price is updated to the current price 45,500. If the price falls 700 basis points from 45,500, the order will be triggered.

Between `12:04:00` and `12:04:45`, a series of market trades cause the price to fall to 42,315. The current price reaches or exceeds the 700 basis points threshold set by the order (`42,315 = 45,500 * 0.93`), causing the order to be triggered and placed on the order book at the LIMIT price of 39,000.

<img alt="image" src="https://user-images.githubusercontent.com/17701918/167370616-17d3295a-3e7c-4314-aa13-ad44e685a311.png" />

### Trailing stop order examples

Assume the current latest price is 40,000.

Place a trailing `STOP_LOSS_LIMIT` BUY order with price 42,000 and trailing delta (`trailingDelta`) of 5%.

```bash
# Without stop price
POST 'https://api.axvn.vn/api/v3/order?symbol=BTCUSDT&side=BUY&type=STOP_LOSS_LIMIT&timeInForce=GTC&quantity=0.01&price=42000&trailingDelta=500&timestamp=<timestamp>&signature=<signature>'

# With stop price 43,000
POST 'https://api.axvn.vn/api/v3/order?symbol=BTCUSDT&side=BUY&type=STOP_LOSS_LIMIT&timeInForce=GTC&quantity=0.01&price=42000&stopPrice=43000&trailingDelta=500&timestamp=<timestamp>&signature=<signature>'
```

Place a trailing `STOP_LOSS_LIMIT` SELL order with price 37,500 and trailing delta (`trailingDelta`) of 2.5%.

```bash
# Without stop price
POST 'https://api.axvn.vn/api/v3/order?symbol=BTCUSDT&side=SELL&type=STOP_LOSS_LIMIT&timeInForce=GTC&quantity=0.01&price=37500&trailingDelta=250&timestamp=<timestamp>&signature=<signature>'

# With stop price 39,000
POST 'https://api.axvn.vn/api/v3/order?symbol=BTCUSDT&side=SELL&type=STOP_LOSS_LIMIT&timeInForce=GTC&quantity=0.01&price=37500&stopPrice=39000&trailingDelta=250&timestamp=<timestamp>&signature=<signature>'
```

Place a trailing `TAKE_PROFIT_LIMIT` BUY order with price 38,000 and trailing delta (`trailingDelta`) of 5%.

```bash
# Without stop price
POST 'https://api.axvn.vn/api/v3/order?symbol=BTCUSDT&side=BUY&type=TAKE_PROFIT_LIMIT&timeInForce=GTC&quantity=0.01&price=38000&trailingDelta=500&timestamp=<timestamp>&signature=<signature>'

# With stop price 36,000
POST 'https://api.axvn.vn/api/v3/order?symbol=BTCUSDT&side=BUY&type=TAKE_PROFIT_LIMIT&timeInForce=GTC&quantity=0.01&price=38000&stopPrice=36000&trailingDelta=500&timestamp=<timestamp>&signature=<signature>'
```

Place a trailing `TAKE_PROFIT_LIMIT` SELL order with price 41,500 and trailing delta (`trailingDelta`) of 1.75%.

```bash
# Without stop price
POST 'https://api.axvn.vn/api/v3/order?symbol=BTCUSDT&side=SELL&type=TAKE_PROFIT_LIMIT&timeInForce=GTC&quantity=0.01&price=41500&trailingDelta=175&timestamp=<timestamp>&signature=<signature>'

# With stop price 42,500
POST 'https://api.axvn.vn/api/v3/order?symbol=BTCUSDT&side=SELL&type=TAKE_PROFIT_LIMIT&timeInForce=GTC&quantity=0.01&price=41500&stopPrice=42500&trailingDelta=175&timestamp=<timestamp>&signature=<signature>'
```
