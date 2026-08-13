# Filters

Filters define a series of trading rules.
There are three categories: symbol filters (`symbol filters`), exchange-level filters (`exchange filters`), and asset filters (`asset filters`).

## Symbol Filters
<a id="price"></a>
### PRICE_FILTER

The price filter is used to validate the `price` parameter in order requests.

* `minPrice` defines the minimum allowed value for `price`/`stopPrice`; disabled when `minPrice` == 0.
* `maxPrice` defines the maximum allowed value for `price`/`stopPrice`; disabled when `maxPrice` == 0.
* `tickSize` defines the increment step for `price`/`stopPrice`; disabled when `tickSize` == 0.

Each of the above can be 0, in which case that constraint is not applied.

An order must satisfy the following conditions to pass this filter:
* `price` >= `minPrice`
* `price` <= `maxPrice`
* `price` % `tickSize` == 0

**/exchangeInfo response format:**
```javascript
{
    "filterType": "PRICE_FILTER",
    "minPrice": "0.00000100",
    "maxPrice": "100000.00000000",
    "tickSize": "0.00000100"
}
```
<a id="percent_price"></a>
### PERCENT_PRICE

The `PERCENT_PRICE` filter defines the valid range for an order's `price` based on the `weighted average of previous trade prices`.

* When a non-empty [reference price](./faqs/price_range_execution_rules_CN.md) exists for the trading pair, the filter validation uses that reference price.
* When no non-empty reference price exists for the trading pair, the filter validation uses the volume-weighted average price over the past `avgPriceMins` minutes.
  * If `avgPriceMins` is 0, the filter validation uses the latest trade price.

An order must satisfy the following conditions to pass this filter:
* `price` <= `average of previous trade prices` * `multiplierUp`
* `price` >= `average of previous trade prices` * `multiplierDown`

**/exchangeInfo response format:**
```javascript
{
    "filterType": "PERCENT_PRICE",
    "multiplierUp": "1.3000",
    "multiplierDown": "0.7000",
    "avgPriceMins": 5
}
```
<a id="percent_price_by_side"></a>
#### PERCENT_PRICE_BY_SIDE

The `PERCENT_PRICE_BY_SIDE` filter defines the valid range for an order's `price` based on the `weighted average of previous trade prices`.

* When a non-empty [reference price](./faqs/price_range_execution_rules_CN.md) exists for the trading pair, the filter validation uses that reference price.
* When no non-empty reference price exists for the trading pair, the filter validation uses the volume-weighted average price over the past `avgPriceMins` minutes.
  * If `avgPriceMins` is 0, the filter validation uses the latest trade price.

`BUY` orders must satisfy the following conditions to pass this filter:

* `price` <= `average of previous trade prices` * `bidMultiplierUp`
* `price` >= `average of previous trade prices` * `bidMultiplierDown`

`SELL` orders must satisfy the following conditions to pass this filter:
* `price` <= `average of previous trade prices` * `askMultiplierUp`
* `price` >= `average of previous trade prices` * `askMultiplierDown`

**/exchangeInfo response format:**
```javascript
{
    "filterType": "PERCENT_PRICE_BY_SIDE",
    "bidMultiplierUp": "1.2",
    "bidMultiplierDown": "0.2",
    "askMultiplierUp": "5",
    "askMultiplierDown": "0.8",
    "avgPriceMins": 1
}
```
<a id="lot_size"></a>
### LOT_SIZE

"Lots" is an auction term. This filter validates the `quantity` parameter in orders. It has three components:

* `minQty` defines the minimum allowed value for `quantity`/`icebergQty`.
* `maxQty` defines the maximum allowed value for `quantity`/`icebergQty`.
* `stepSize` defines the allowed step increment for `quantity`/`icebergQty`.

An order must satisfy the following conditions to pass this filter:

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* `quantity` % `stepSize` == 0

**/exchangeInfo response format:**
```javascript
{
    "filterType": "LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
}
```
<a id="min_notional"></a>
### MIN_NOTIONAL

The `MIN_NOTIONAL` filter defines the minimum notional value (trade amount) allowed for orders on a trading pair.

* The notional value of an order is `price` * `quantity`.
* `applyToMarket` determines whether the `MIN_NOTIONAL` filter also applies to `MARKET` orders.
  * Since `MARKET` orders have no `price`, the `average of previous trade prices` is used.
    * When a non-empty [reference price](./faqs/price_range_execution_rules_CN.md) exists for the trading pair, the filter validation uses that reference price as the `price`.
    * When no non-empty reference price exists for the trading pair, the volume-weighted average price over the past `avgPriceMins` minutes is used as the `price`.
      * If `avgPriceMins` is 0, the latest trade price is used as the `price`.

An order must satisfy the following condition to pass this filter:
* `price` * `quantity` >= `minNotional`

**/exchangeInfo response format:**
```javascript
{
    "filterType": "MIN_NOTIONAL",
    "minNotional": "0.00100000",
    "applyToMarket": true,
    "avgPriceMins": 5
}
```
<a id="notional"></a>
### NOTIONAL

The `NOTIONAL` filter defines the notional value range within which orders can be placed on a trading pair.

* `applyMinToMarket` defines whether `minNotional` applies to market orders (`MARKET`).
* `applyMaxToMarket` defines whether `maxNotional` applies to market orders (`MARKET`).
  * Since `MARKET` orders have no `price`, the `average of previous trade prices` is used.
    * When a non-empty [reference price](./faqs/price_range_execution_rules_CN.md) exists for the trading pair, the filter validation uses that reference price as the `price`.
    * When no non-empty reference price exists for the trading pair, the volume-weighted average price over the past `avgPriceMins` minutes is used as the `price`.
      * If `avgPriceMins` is 0, the latest trade price is used as the `price`.

An order must satisfy the following conditions to pass this filter:
* `price` * `quantity` <= `maxNotional`
* `price` * `quantity` >= `minNotional`

**/exchangeInfo response format:**

```javascript
{
    "filterType": "NOTIONAL",
    "minNotional": "10.00000000",
    "applyMinToMarket": false,
    "maxNotional": "10000.00000000",
    "applyMaxToMarket": false,
    "avgPriceMins": 5
}
```

### ICEBERG_PARTS

`ICEBERG_PARTS` represents the maximum number of sub-orders an iceberg order can be split into.
Calculated as `ceil(qty / icebergQty)`.

**/exchangeInfo response format:**
```javascript
{
    "filterType": "ICEBERG_PARTS",
    "limit": 10
}
```

<a id="market_lot_size"></a>
### MARKET_LOT_SIZE

The `MARKET_LOT_SIZE` filter defines the `quantity` (i.e., "lot size" in auction terms) rules for `MARKET` orders on a trading pair. It has 3 components:

* `minQty` defines the minimum allowed `quantity`.
* `maxQty` defines the maximum allowed quantity.
* `stepSize` defines the interval by which quantity can be increased/decreased.

To pass the `market lot size` filter, `quantity` must satisfy:

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* `quantity` % `stepSize` == 0

**/exchangeInfo response format:**
```javascript
{
    "filterType": "MARKET_LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
}
```
<a id="max_num_orders"></a>
### MAX_NUM_ORDERS

Defines the maximum number of open orders allowed on a trading pair (excluding closed orders).
Both regular orders and conditional orders are counted.

**/exchangeInfo response format:**
```javascript
{
    "filterType": "MAX_NUM_ORDERS",
    "maxNumOrders": 25
}
```

### MAX_NUM_ALGO_ORDERS

The `MAX_NUM_ALGO_ORDERS` filter defines the maximum number of "algo" orders an account can have open on a trading pair.
"Algo" orders are `STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, and `TAKE_PROFIT_LIMIT` orders.

**/exchangeInfo response format:**
```javascript
{
    "filterType": "MAX_NUM_ALGO_ORDERS",
    "maxNumAlgoOrders": 5
}
```

### MAX_NUM_ICEBERG_ORDERS

The `MAX_NUM_ICEBERG_ORDERS` filter defines the maximum number of `ICEBERG` orders an account can have open on a trading pair.
An `ICEBERG` order is any order with an `icebergQty` greater than 0.

**/exchangeInfo response format:**
```javascript
{
    "filterType": "MAX_NUM_ICEBERG_ORDERS",
    "maxNumIcebergOrders": 5
}
```

### MAX_POSITION

This filter defines the maximum position (in base asset) an account is allowed to hold. A user's position is defined as the sum of the following:
1. Available balance of the `base asset`
2. Locked balance of the `base asset`
3. Total quantity of all open buy orders

If a user's position exceeds the maximum allowed position, buy orders will be rejected.

If an order's quantity (`quantity`) may cause the held position to overflow, the `MAX_POSITION` filter will be triggered.

**/exchangeInfo response format:**
```javascript
{
    "filterType": "MAX_POSITION",
    "maxPosition": "10.00000000"
}
```

### TRAILING_DELTA


This filter defines the maximum and minimum values for the [`trailingDelta`](faqs/trailing-stop-faq_CN.md) parameter.

To place a trailing stop order, the following conditions must be met:

For `STOP_LOSS BUY`, `STOP_LOSS_LIMIT_BUY`, `TAKE_PROFIT SELL`, and `TAKE_PROFIT_LIMIT SELL` orders:

* `trailingDelta` >= `minTrailingAboveDelta`
* `trailingDelta` <= `maxTrailingAboveDelta`

For `STOP_LOSS SELL`, `STOP_LOSS_LIMIT SELL`, `TAKE_PROFIT BUY`, and `TAKE_PROFIT_LIMIT BUY` orders:

* `trailingDelta` >= `minTrailingBelowDelta`
* `trailingDelta` <= `maxTrailingBelowDelta`

 **/exchangeInfo format:**
```javascript
{
    "filterType": "TRAILING_DELTA",
    "minTrailingAboveDelta": 10,
    "maxTrailingAboveDelta": 2000,
    "minTrailingBelowDelta": 10,
    "maxTrailingBelowDelta": 2000
}
```

<a id="max_num_order_amends"></a>
### MAX_NUM_ORDER_AMENDS

This filter defines the maximum number of order amendments allowed for a specified trading pair.

If a single order is amended too many times, you will receive error code `-2038`.

**/exchangeInfo format:**

```javascript
{
    "filterType": "MAX_NUM_ORDER_AMENDS",
    "maxNumOrderAmends": 10
}
```

### MAX_NUM_ORDER_LISTS

This filter defines the maximum number of open order lists an account can hold on a trading pair. Note that an OTOCO counts as one order list.

**/exchangeInfo format:**

```javascript
{
    "filterType": "MAX_NUM_ORDER_LISTS",
    "maxNumOrderLists": 20
}
```


## Exchange Filters
### EXCHANGE_MAX_NUM_ORDERS

The `EXCHANGE_MAX_NUM_ORDERS` filter defines the maximum number of orders an account can have open on the exchange.
Note that this filter counts both "algo" orders and regular orders.

**/exchangeInfo response format:**
```javascript
{
    "filterType": "EXCHANGE_MAX_NUM_ORDERS",
    "maxNumOrders": 1000
}
```

### EXCHANGE_MAX_NUM_ALGO_ORDERS

The `EXCHANGE_MAX_NUM_ALGO_ORDERS` filter defines the maximum number of "algo" orders an account can have open on the exchange.
"Algo" orders are `STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, and `TAKE_PROFIT_LIMIT` orders.

**/exchangeInfo response format:**
```javascript
{
    "filterType": "EXCHANGE_MAX_NUM_ALGO_ORDERS",
    "maxNumAlgoOrders": 200
}
```

### EXCHANGE_MAX_NUM_ICEBERG_ORDERS

This filter defines the maximum number of iceberg orders an account is allowed to hold.


**/exchangeInfo response format:**

```javascript
{
    "filterType": "EXCHANGE_MAX_NUM_ICEBERG_ORDERS",
    "maxNumIcebergOrders": 10000
}
```

### EXCHANGE_MAX_NUM_ORDER_LISTS

This filter defines the maximum number of open order lists an account is allowed to hold. Note that an OTOCO counts as one order list.

**/exchangeInfo format:**

```javascript
{
    "filterType": "EXCHANGE_MAX_NUM_ORDER_LISTS",
    "maxNumOrderLists": 20
}
```


## Asset Filters
### MAX_ASSET

The `MAX_ASSET` filter defines the maximum quantity of an asset that an account can trade in a single order.

* When the asset is the base asset of a trading pair, the limit applies to the order quantity.
* When the asset is the quote asset of a trading pair, the limit applies to the notional value of the order.
* For example, a MAX_ASSET filter on USDC applies to all trading pairs where USDC is the base or quote asset, such as:
  * USDCAXVN
  * AXVNUSDC

**/myFilters format:**

```javascript
{
    "filterType": "MAX_ASSET",
    "asset": "USDC",
    "limit": "42.00000000"
}
```
