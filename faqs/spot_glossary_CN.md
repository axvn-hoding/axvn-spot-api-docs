# Spot Trading API Glossary

**Disclaimer:** This glossary applies only to the Spot trading (`SPOT`) API; terms used in Futures, Options, or other API contexts may have different meanings.

### A

`ACK`
* An enum value for `newOrderRespType`. When set, the order placement response only includes the following fields: `symbol`, `orderId`, `orderListId`, `clientOrderId`, and `transactTime`.

`aggTrade`
* Aggregate trade; this trade aggregates trade information generated at the same time by the same `taker` at the same price.

allocation
* Here, an allocation refers to the process of transferring assets from the exchange to a personal account (e.g. when an order is filled via SOR instead of a direct trade).

`allocationId`
* A unique identifier used to identify an allocation made on a specific trading pair.

`allocationType`
* Refer to [Allocation Type](../enums_CN.md#allocationtype)

`askPrice`
* The lowest price from the "sell" side returned by a `ticker` request.

`askQty`
* The total quantity available from the "sell" side at the lowest price, as returned by a `ticker` request.

`asks`
* Sell orders

`avgPrice`
* Represents the average price over the corresponding N minutes.

---

### B

`baseAsset`
* Base asset; refers to the first asset in a trading pair (e.g. `BTC` in `BTCUSDT`), representing the asset being bought or sold.

`baseAssetPrecision`
* Base asset precision; a field in the Exchange Information response that represents the maximum number of decimal places allowed for the base asset (`baseAsset`).

`baseCommissionPrecision`
* A field in the Exchange Information response indicating the maximum number of decimal places allowed for commissions denominated in the base asset.

`bidPrice`
* The highest price from the "buy" side returned by a `ticker` request.

`bidQty`
* The total quantity available from the "buy" side at the highest price, as returned by a `ticker` request.

`bids`
* Buy orders.

`BREAK`
* A trading status for a trading pair, indicating that the pair cannot be traded. Trading pairs in this status do not generate market data.

`BUY`
* An enum value for `side`, indicating that the user intends to purchase an asset (e.g. `BTC`).

---

### C

`CANCELED`
* An order status indicating that the order was cancelled by the user.

`cancelReplaceMode`
* A parameter for the cancel-replace order endpoint, defining whether to continue placing a new order if the cancellation request fails.

`CANCEL_ONLY`
* A trading status indicating that users can **only cancel and amend orders** on that trading pair.
* Market data (except candlestick charts) is still generated under this trading status.

`clientOrderId`
* Used in order requests; this field allows users to set a custom value to help track orders.

`commission`
* Commission

`commissionAsset`
* The asset used to calculate commissions.

Counter Order Id
* A field in user data stream execution reports, indicating the counterpart order in a blocked self-trade match.

Counter Symbol
* A field in user data stream execution reports, indicating the trading pair used by the counterpart order in a blocked self-trade match.

`cummulativeQuoteQty`
* The sum of all price (`price`) multiplied by quantity (`qty`) for all trades in the order's fill history.

---

### D

Data Source
* Where data is retrieved from after sending a request, such as a database, cache, etc.

---

### E

`executedQty`
* The quantity filled in the order.

`EXPIRED`
* An order status indicating that the order was cancelled due to trading rules or directly by the exchange.

`EXPIRED_IN_MATCH`
* An order status indicating that the order expired due to STP (e.g. an order with `EXPIRE_TAKER` matched with an order on the order book belonging to the same account or same `tradeGroupId`).

---

### F

`filters`
* Filters; used to define trading rules.

`FOK` / Fill or Kill
* An enum value for `timeInForce`, requiring the order to be fully filled upon placement, otherwise it is cancelled.

`free`
* The user's available balance that can be used for trading or withdrawal.

`FULL`
* An enum value for `newOrderRespType`. When set on the order placement endpoint, the response returns all trade information including fills (`fills`).

---

### G

`GTC` / Good Til Canceled
* An enum value for `timeInForce`, indicating the order remains active until fully filled or cancelled.

---

### H

`HALT`
* A trading status for a trading pair, indicating that trading is in emergency suspension. Market data is still generated in this state.

---

### I

`intervalNum`
* Represents the interval duration. For example, if `interval` is `SECOND` and `intervalNum` is 5, it means an interval of every 5 seconds.

`IOC` / Immediate or Canceled
* An enum value for `timeInForce`, indicating the order will fill as much as possible and any unfilled portion will be cancelled by the exchange.

`isBestMatch`
* Indicates whether the trade price was the best available price at that time.

`isBuyerMaker`
* Indicates whether the buyer in a trade is the market maker (`Maker`).

`isWorking`
* Indicates whether the order appears on the order book.

---

### K

`kline`
* Candlestick; includes the open, close, high, low, volume, and other market data for a given time period. Also commonly referred to as a candlestick chart.

---

### L

Last Prevented Quantity
* The last quantity prevented from trading. This is only visible when an order expires due to STP triggering.

`lastPrice`
* The execution price of the most recent trade.

`lastQty`
* The total quantity traded at `lastPrice`.

`LIMIT`
* A limit order; an order type where the order will be filled at the specified price or better.

`LIMIT_MAKER`
* An order type that guarantees the order will be a maker order (`MAKER`) and will not be immediately filled as a `TAKER`.

`limitClientOrderId`
* A parameter for the OCO order placement endpoint, allowing users to set a custom ID to identify the `LIMIT_MAKER` order within the OCO.

`listClientOrderId`
* A parameter for the OCO order placement endpoint, allowing users to set a custom ID to identify the OCO order.

`locked`
* Represents the amount of a user's asset balance currently locked in open orders or reserved by other systems.

---

### M

`MARKET`
* An order type; the order will be filled as completely as possible by the system, and any unfilled portion will be cancelled if the market lacks liquidity.

Matching Engine
* In the context of Data Source, refers to where data is retrieved from when a request is processed.
* Can also refer to the backend system that processes all requests and matches all orders.

Match Type
* A field in order responses or execution reports, indicating whether the order was filled via [Smart Order Routing (SOR)](./sor_faq_CN.md).

Memory
* In the context of Data Source, refers to data stored in the system's internal cache.

---

### N

`NEW`
* An order status indicating that the order was successfully submitted to the trading engine.

`newClientOrderId`
* A parameter in order-related requests (place order, cancel order, etc.); in the response, this value will be set as the `clientOrderId`.

Notional value
* The notional value of an order, equal to `price` * `qty`.

---

### O

`OCO`

* One-Cancels-the-Other; an order that allows users to submit a combination of orders simultaneously, such as a limit maker order (`LIMIT_MAKER`) and a stop-loss order (`STOP_LOSS` or `STOP_LOSS_LIMIT`). When one order is executed, the other is automatically cancelled.

`OPO`
* [One-Pays-the-Other](opo_CN.md), a special subset of OTO.
* When the working order is fully filled, the cumulative received quantity will be used as the quantity of the pending order.

`OPOCO`
* [One-Pays-the-Other](opo_CN.md), a special subset of OTOCO.
* When the working order is fully filled, the cumulative received quantity will be used as the quantity of the pending OCO order combination.

Order Amend Keep Priority
* Refer to [Order Amend Keep Priority](order_amend_keep_priority_CN.md)

Order Book
* Order book; includes all current buy and sell resting orders in the market.

Order List
* Order list; groups multiple orders together as a single unit. See `OCO` and/or `OTO`.

`orderId`
* A unique identifier for an order.

`origQty`
* The original quantity specified in the order placement request.

`origClientOrderId`
* The value set in `clientOrderId` by the user in a query or cancel order request.

`OTO`
* One-Triggers-the-Other; this order list contains a working order and a pending order.
* The pending order is only automatically placed when the working order is fully filled.

`OTOCO`
* One-Triggers-a-One-Cancels-the-Other; this order list contains a working order and a pending OCO order.
* The pending order is only automatically placed when the working order is fully filled.

---

### P

`PARTIALLY_FILLED`
* An order status indicating that the order has been partially filled.

Pending order
* An order within an order list that is only placed on the order book when the corresponding working order is fully filled.
* Each order list can contain one pending order, or two pending orders that form an `OCO`.
* In the case of a single order, almost any order type is supported, except `MARKET` orders using `quoteOrderQty`.

`PENDING_NEW`
* An order `status`; indicates that the engine has accepted the pending order of an order list, but the pending order has not yet been placed on the order book.

Prevented execution price
* A field in user data stream execution reports, indicating the price of a blocked self-trade. See [Self Trade Prevention (STP) FAQ](stp_faq_CN.md).

Prevented execution quantity
* A field in user data stream execution reports, indicating the order quantity of a blocked self-trade. See [Self Trade Prevention (STP) FAQ](stp_faq_CN.md).

Prevented execution quote quantity
* A field in user data stream execution reports, indicating the quote order quantity of a blocked self-trade. See [Self Trade Prevention (STP) FAQ](stp_faq_CN.md).

`preventedQuantity`
* The quantity of the order that expired due to STP.

Prevented Match
* When an order expires due to STP, a "prevented match" records that event.

`preventedMatchId`
* Used in combination with `symbol` to query expired orders that were prevented due to STP.

---

### Q

`quantity`
* Order quantity; the amount of base asset (`base asset`) in a buy or sell order.

`quoteAsset`
* Quote asset; the second asset in a trading pair, e.g. `USDT` in the pair `BTCUSDT`.

`quoteAssetPrecision`
* A field in the Exchange Information response indicating the maximum number of decimal places allowed for the `quoteAsset`.

`quoteCommissionPrecision`
* A field in the Exchange Information response indicating the maximum number of decimal places allowed for commissions denominated in the `quoteAsset`.

`quoteOrderQty`
* The quantity parameter used in market order (`MARKET`) placements for placing a reverse market order.

`quoteQty`
* Notional value; equals the quantity (`qty`) multiplied by the price (`price`) in an order.

---

### R

`recvWindow`
* An API parameter, in milliseconds; used to set the validity period of a request starting from `timestamp`.

`RESULT`
* An enum value for `newOrderRespType`. When used on the order placement endpoint, the response returns all values except the fills (`fills`).

Reverse `MARKET` order
* A reverse market order; placing a market order using `quoteOrderQty` instead of `quantity`.

---

### S

Self Trade Prevention (STP)
* Self Trade Prevention; this feature prevents orders from being matched against orders from the same account or accounts under the same `tradeGroupId`. Read the [Self Trade Prevention (STP) FAQ](./stp_faq_CN.md) for more details.

`selfTradePreventionMode`
* This parameter tells the system how to handle an order if a self-trade situation occurs.

`SELL`
* An enum value for `side`, used when the user wants to sell an asset.

Smart Order Routing (SOR)
* Smart Order Routing; uses interchangeable quote assets (`quote asset`) to improve liquidity. Read the [SOR FAQ](./sor_faq_CN.md) for more details.

`specialCommissionForOrder`/`specialCommission`
* Refer to [Commission Rate](commission_faq_CN.md)

`SPOT`
* Spot trading; in this type of trading, the assets bought or sold are credited immediately.

`standardCommissionForOrder`/`standardCommission`
* Refer to [Commission Rate](commission_faq_CN.md)

`stopClientOrderId`
* Used in the OCO order placement endpoint; this ID can be used to identify the `STOP_LOSS` or `STOP_LOSS_LIMIT` order within the OCO.

`stopPrice`
* Used to set the trigger price for conditional orders (e.g. `STOP_LOSS`, `TAKE_PROFIT`); once this price is triggered, the order is placed on the order book (`OrderBook`).
* Used to set the trigger price for trailing stop orders; once this price is triggered, the order begins tracking.

`STOP_LOSS`
* Stop-loss order; a conditional order that is executed as a market order (`MARKET`) when the market price reaches the `stopPrice`.

`STOP_LOSS_LIMIT`
* Stop-loss limit order; a conditional order that is executed as a limit order (`LIMIT`) when the market price reaches the `stopPrice`.

`strategyId`
* Strategy order ID; used to associate this order with a corresponding trading strategy.

`strategyType`
* Strategy order type; used to indicate the trading strategy associated with this order.

`symbol`
* Trading pair; composed of a base asset (`base asset`) and a quote asset (`quote asset`).

---

### T

`TAKE_PROFIT`
* Take-profit order; when the market price reaches the `stopPrice`, this order is executed as a market order (`MARKET`).

`TAKE_PROFIT_LIMIT`
* Take-profit limit order; when the market price reaches the `stopPrice`, this order is executed as a limit order (`LIMIT`).

`taxCommissionForOrder`/`taxCommission`
* Refer to [Commission Rate](commission_faq_CN.md)

`ticker`
* Used to report price changes and other market information over a given time period.

`time`
* For trade/allocation queries: the time at which the trade/allocation was executed.
* For order queries: the time at which the order was created.

`timeInForce`
* Defines the time validity of an order, indicating how long the order will remain on the order book.
* Supported values include: `GTC`, `IOC`, and `FOK`.

`tradeGroupId`
* A group of accounts belonging to the same trade group.

`TRADING`
* A trading status indicating that a trading pair is available for trading.

`trailingDelta`
* Used to define the price delta at which a trailing stop order is triggered.

`trailingTime`
* The time at which a trailing order is activated and begins tracking price movements.

`transactTime`
* The time of an order update: placement, fill, or cancellation. By default, this field (and all timestamp-related fields) is in milliseconds in JSON responses.

---

### U

`uiKlines`
* Candlestick data optimized for front-end display.

`updateTime`
* The last update time of the order. By default, this field (and all timestamp-related fields) is in milliseconds in JSON responses.

User Data Stream
* Pushes timely personal user information via WebSocket, including account balance changes, order updates, etc. Read [User Data Stream](../user-data-stream_CN.md) for more details.

`usedSor`
* Indicates whether the order was submitted via [Smart Order Routing (SOR)](sor_faq_CN.md).

---

### W

`weightedAveragePrice`
* Volume-weighted average price; the average price calculated by weighting each trade's price by its volume over the past N minutes.

`workingFloor`
* Working floor; this field is used to indicate whether the order was filled via SOR or on the order book to which the order was submitted.

Working order
* An order within an order list that is placed on the order book immediately. When this order is fully filled, it triggers the automatic placement of one or more pending orders.
* An order belonging to an order list can only be a single `LIMIT` or `LIMIT_MAKER` order type.

`workingTime`
* Indicates when the order was added to the order book.
---

### X

`X-MBX-ORDER-COUNT-XX`
* A custom value in the response header indicating the remaining order rate limit quota for the current user.

`X-MBX-USED-WEIGHT-XX`
* A custom value in the response header indicating the remaining request weight quota for the current IP within a given time period.
