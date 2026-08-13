# Error Codes

Errors returned by Axvn REST API (including wapi) contain two parts: error code and error message. Error codes are broad categories; one error code may correspond to multiple different error messages.
The following is a complete error code example:
```javascript
{
    "code": -1121,
    "msg": "Invalid symbol."
}
```

## 10xx - Server or Network Issues
### -1000 Unknown error
 * Unknown error

### -1001 Disconnected
 * Usually an internal error; generally resolved by retrying.

### -1002 Unauthorized
 * Please check your (API) permissions.

### -1003 Too many requests
 * Too many requests queued.
 * Request weight too high; current limit is %s request weight per %s. Please use Websocket Streams for real-time updates to avoid polling the API.
 * Request weight too high; IP banned until %s. Please use Websocket Streams for real-time updates to avoid being banned.

### -1006 Unexpected response
 * An unexpected message was received from the internal system; order placement status is unknown.

<a id="-1007-timeout"></a>
### -1007 Timeout
 * Backend service timed out; order placement status is unknown.

### -1008 SERVER_BUSY
  * The Spot trading server is currently overloaded due to other requests. Please retry in a few minutes.

### -1013 Invalid message
  * Request rejected by the API (in this case, the request did not reach the matching engine).
  * Potential error messages can be found in [Filter Failures](#filter-failures) or [Order Placement Failures](#other-errors).

### -1014 Unsupported order parameter combination
 * Unsupported order parameter combination.

### -1015 Too many orders
 * Too many new orders (or cancellations).

### -1016 Service shutdown
 * Service is offline.

### -1020 Unsupported operation
 * Unsupported operation.

### -1021 Timestamp synchronization issue
 * Latency is too high; the server determined the request timed out based on the timestamp in the request. Please improve network conditions or increase recvWindow.
 * Timestamp offset too large; the server determined the client time is more than 1 second ahead of the server time based on the request timestamp. (This parameter cannot be adjusted by the client.)

<a id="-1022-invalid_signature"></a>
### -1022 Invalid signature
 * The signature in the request does not match the signature calculated by the server according to the rules. This is usually because the API secret used in the client code is incorrect.

### -1033 Comp ID in use
 * `SenderCompId(49)` is currently in use. Concurrent use of the same SenderCompId within one account is not allowed.

### -1034 Too many connections
 * Too many concurrent connections; current limit is '%s'.
 * Too many connection attempts for account; current limit is  %s per '%s'.
 * Too many connection attempts from IP; current limit is %s

### -1035 Session logout
 * Please send [Logout`<5>`](fix-api_CN.md#logout) message to close the session.

## 11xx - Request Content Issues
### -1100 Illegal characters
 * Illegal characters found in a parameter.
 * Illegal characters found in parameter '%s'; legal range is '%s'.

### -1101 Too many parameters
 * Too many parameters sent for this endpoint.
 * Too many parameters; expected '%s' and received '%s'.
 * Duplicate values for a parameter detected.

### -1102 Missing required parameter
 * A mandatory parameter was not sent, was empty/null, or malformed.
 * Mandatory parameter '%s' was not sent, was empty/null, or malformed.
 * Param '%s' or '%s' must be sent, but both were empty/null!
 * Required tag '%s' missing.
 * Field value was empty or malformed.
 * '%s' contains unexpected value. Cannot be greater than %s.

### -1103 Unrecognized parameter
 * An unknown parameter was sent.
 * Undefined Tag.

### -1104 Redundant parameters
 * Not all sent parameters were read.
 * Not all sent parameters were read; read '%s' parameter(s) but was sent '%s'.

### -1105 Empty parameter (parameter name only)
 * A parameter was empty.
 * Parameter '%s' was empty.

### -1106 Non-required parameter
 * A parameter was sent when not required.
 * Parameter '%s' sent when not required.
 * A tag '%s' was sent when not required.

### -1108 Parameter overflow
 * Parameter '%s' overflowed.

### -1111 Too much precision
 * Parameter '%s' has too much precision.

### -1112 Empty order book
 * No orders on book for symbol.

### -1114 TIF parameter sent unnecessarily
 * TimeInForce parameter sent when not required.

### -1115 Invalid TIF parameter
 * Invalid timeInForce.

### -1116 Invalid order type
 * Invalid orderType.

### -1117 Invalid order side
 * Invalid side.

### -1118 Empty newClientOrderId
 * New client order ID was empty.

### -1119 Empty originalClientOrderId
 * Original client order ID was empty.

### -1120 Invalid interval
 * Invalid interval.

### -1121 Invalid symbol
 * Invalid symbol.

### -1122 Invalid symbol status
  * Invalid symbolStatus.

### -1125 Invalid listenKey
 * This listenKey does not exist.

### -1127 Query interval too long
 * Lookup interval is too big.
 * More than %s hours between startTime and endTime.

### -1128 Invalid optional parameter combination
 * Combination of optional parameters invalid.
 * Combination of optional fields invalid. Recommendation: '%s' and '%s' must both be sent.
 * Fields [%s] must be sent together or omitted entirely.
 * Invalid 'MDEntryType (269)' combination. BID and OFFER must be requested together.
 * Conflicting fields: ['%s'...]

### -1130 Invalid parameter value
 * Invalid data sent for a parameter.
 * Data sent for parameter '%s' is not valid.

### -1134 strategyType does not meet requirements
 * `strategyType` was less than 1000000.
 * `TargetStrategy (847)` was less than 1000000.

### -1135 Invalid JSON
 * Invalid JSON Request
 * JSON sent for parameter '%s' is not valid

### -1139 Invalid ticker type
 * Invalid ticker type.

### -1145 Invalid cancel restriction
 * `cancelRestrictions` has to be either `ONLY_NEW` or `ONLY_PARTIALLY_FILLED`.

### -1151 Duplicate symbol
 * Symbol is present multiple times in the list.

### -1152 Invalid SBE header
* Invalid `X-MBX-SBE` header; expected `<SCHEMA_ID>:<VERSION>`.
* Invalid SBE message header.

### -1153 Unsupported SCHEMA_ID
* Unsupported SBE schema ID or version specified in the `X-MBX-SBE` header.
* Invalid SBE schema ID or version specified.

### -1155 SBE not enabled
* SBE is not enabled.

### -1158 OCO order type rejected
* Order type not supported in OCO.
* If the order type provided in the `aboveType` and/or `belowType` is not supported.

### -1160 OCO order icebergQty and timeInForce combination issue
* Parameter '%s' is not supported if `aboveTimeInForce`/`belowTimeInForce` is not GTC.
* If the order type for the above or below leg is `STOP_LOSS_LIMIT`, and `icebergQty` is provided for that leg, the `timeInForce` has to be `GTC` else it will throw an error.
* `TimeInForce (59)` must be `GTC (1)` when `MaxFloor (111)` is used.

### -1161 Deprecated schema
* Unable to encode the response in SBE schema 'x'. Please use schema 'y' or higher.

### -1165 Buy OCO limit order must be below
* A limit order in a buy OCO must be below.

### -1166 Sell OCO limit order must be above
* A limit order in a sell OCO must be above.

### -1168 Both OCO orders cannot be limit orders
* At least one OCO order must be contingent.

### -1169 Invalid tag
 * Invalid tag number.

### -1170 Tag not defined
 * Tag '%s' not defined for this message type.

### -1171 Tag appears more than once
 * Tag '%s' appears more than once.

### -1172 Tag out of required order
 * Tag '%s' specified out of required order.

### -1173 Repeating group fields out of order
 * Repeating group '%s' fields out of order.

### -1174 Invalid component
 * Component '%s' is incorrectly populated on '%s' order. Recommendation: '%s'

### -1175 Sequence number reset error
 * Continuation of sequence numbers to new session is currently unsupported. Sequence numbers must be reset for each new session.

### -1176 Already logged on
 * [Logon`<A>`](fix-api_CN.md#logon-main) should only be sent once.

### -1177 Malformed message
 * `CheckSum(10)` contains an incorrect value.
 * `BeginString (8)` is not the first tag in a message.
 * `MsgType (35)` is not the third tag in a message.
 * `BodyLength (9)` does not contain the correct byte count.
 * Only printable ASCII characters and SOH (Start of Header) are allowed.
 * Tag specified without a value.
 * Invalid encodingType.

### -1178 Incorrect Comp ID
 * `SenderCompId(49)` contains an incorrect value. The SenderCompID value should not change throughout the lifetime of a session.

### -1179 Incorrect sequence number
 * `MsgSeqNum(34)` contains an unexpected value. Expected: '%d'.

### -1180 Logon message error
 * [Logon`<A>`](fix-api_CN.md#logon-main) must be the first message in the session.

### -1181 Too many messages
 * Too many messages; current limit is '%d' messages per '%s'.

### -1182 Conflicting parameter combination
 * Conflicting fields: [%s]

### -1183 Not allowed in Drop Copy sessions
 * Requested operation is not allowed in DropCopy sessions.

### -1184 Drop Copy sessions not allowed
 * DropCopy sessions are not supported on this server. Please reconnect to a drop copy server.

### -1185 Drop Copy session required
 * Only DropCopy sessions are supported on this server. Either reconnect to order entry server or send `DropCopyFlag (9406)` field.

### -1186 Not allowed in order entry sessions
* Requested operation is not allowed in order entry sessions.

### -1187 Not allowed in market data sessions
* Requested operation is not allowed in market data sessions.

### -1188 Incorrect NumInGroup count
* Incorrect NumInGroup count for repeating group '%s'.

### -1189 Group contains duplicate entries
* Group '%s' contains duplicate entries.

### -1190 Invalid request ID
* `MDReqID (262)` contains a suaxvnscription request id that is already in use on this connection.
* `MDReqID (262)` contains an unsuaxvnscription request id that does not match any active suaxvnscription.

### -1191 Too many subscriptions
* Too many suaxvnscriptions. Connection may create up to '%s' suaxvnscriptions at a time.
* Similar suaxvnscription is already active on this connection. Symbol='%s', active suaxvnscription id: '%s'.

### -1194 Invalid time unit
* Invalid value for time unit; expected either MICROSECOND or MILLISECOND.

### -1196 Buy OCO stop loss limit order must be above
* A stop loss order in a buy OCO must be above.

### -1197 Sell OCO stop loss limit order must be below
* A stop loss order in a sell OCO must be below.

### -1198 Buy OCO take profit order must be below
* A take profit order in a buy OCO must be below.

### -1199 Sell OCO take profit order must be above
* A take profit order in a sell OCO must be above.

### -1210 Invalid peg price type
* Invalid pegPriceType.

### -1211 Invalid peg offset type
* Invalid pegOffsetType.

<a id="-1220-symbol_does_not_match_status"></a>
### -1220 Symbol does not match status
* The symbol's status does not match the requested symbolStatus.

### -1221 Invalid field in SBE message
* Invalid/missing field(s) in SBE message.

### -1222 OPO working order must be a bid
* Working order in an OPO list must be a bid.

### -1223 OPO pending orders must be asks
* Pending orders in an OPO list must be asks.

### -1224 Working order missing required tag
* Working order must include the '{param}' tag.

### -1225 Pending orders contain unnecessary tag
* Pending orders should not include the '%s' tag.

### -2010 New order rejected
 * NEW_ORDER_REJECTED

### -2011 Cancel order rejected
 * CANCEL_REJECTED

### -2013 Order does not exist
 * Order does not exist.

### -2014 Invalid API Key format
 * API-key format invalid.

### -2015 Invalid API Key, IP, or permissions
 * Invalid API-key, IP, or permissions for action.

### -2016 No trading window
 * No trading window could be found for the symbol. Try ticker/24hrs instead.

<a id="-2026-order_archived"></a>
### -2026 Order archived
  * Order was canceled or expired with no executed qty over 90 days ago and has been archived.

### -2035 Active subscription
  * User Data Stream suaxvnscription already active.

### -2036 Inactive subscription
  * User Data Stream suaxvnscription not active.

### -2039 Invalid ClientOrderId
  * Client order ID is not correct for this order ID.

### -2042 Maximum subscription ID
* Maximum suaxvnscription ID reached for this connection.

<a id="-2043-no_reference_price"></a>
### -2043 No reference price
* This symbol doesn't have a reference price.

<a id="other-errors"></a>

## Messages for -1010 ERROR_MSG_RECEIVED, -2010 NEW_ORDER_REJECTED, -2011 CANCEL_REJECTED, and -2038 ORDER_AMEND_KEEP_PRIORITY_REJECTED
These error codes are thrown by the matching engine.
The following messages will indicate the specific error:

Error Message                                                    | Description
------------                                                     | ------------
"Unknown order sent."                                            | Order not found (based on `orderId`, `clOrdId`, `origClOrdId` sent in the request).
"Duplicate order sent."                                          | Client-defined order ID is duplicated.
"Market is closed."                                              | The trading pair has trading closed.
"Account has insufficient balance for requested action."         | Insufficient account balance.
"Market orders are not supported for this symbol."               | MARKET orders are not supported for this symbol.
"Iceberg orders are not supported for this symbol."              | Iceberg orders are not supported for this symbol.
"Stop loss orders are not supported for this symbol."            | Stop loss orders are not supported for this symbol.
"Stop loss limit orders are not supported for this symbol."      | Stop loss limit orders are not supported for this symbol.
"Take profit orders are not supported for this symbol."          | Take profit orders are not supported for this symbol.
"Take profit limit orders are not supported for this symbol."    | Take profit limit orders are not supported for this symbol.
"Order amend is not supported for this symbol."                  | Order Amend Keep Priority is not supported for this symbol.
"Price * QTY is zero or less."                                   | Order amount must be greater than 0.
"IcebergQty exceeds QTY."                                        | The quantity of each individual iceberg order must be less than the total order quantity.
"This action is disabled on this account."                       | Contact customer support; certain operations have been disabled for this account.
"This account may not place or cancel orders."                   | Contact customer support: trading operations have been disabled for this account.
"Unsupported order combination"                                  | Some parameters like `orderType`, `timeInForce`, `stopPrice`, `icebergQty` must/must not be provided when other parameters take certain values.
"Order would trigger immediately."                               | Take profit/stop loss orders must be triggered in the future. If the condition is too weak and the current market price would trigger it immediately (usually a mistake in setting the condition), this error will be returned.
"Cancel order is invalid. Check origClOrdId and orderId."        | Canceling an order must provide either `origClOrdId` or `orderId`.
"Order would immediately match and take."                        | A `LIMIT_MAKER` order would become a taker according to the rules, which is not allowed.
"The relationship of the prices for the orders is not correct."  | The prices set in an `OCO` order do not comply with the quoting rules:<br/> Please refer to the following examples: <br/> `BUY`: `LIMIT_MAKER` `price` < Last Traded Price < `stopPrice` <br/> `SELL`: `LIMIT_MAKER` `price` > Last Traded Price > `stopPrice` <br/>
"OCO orders are not supported for this symbol"                   | `OCO` orders are not supported for this symbol.
"Quote order qty market orders are not support for this symbol." | For this symbol, market orders do not support the `quoteOrderQty` parameter.
"Trailing stop orders are not supported for this symbol."        | `trailingDelta` is not supported for this symbol.
"Order cancel-replace is not supported for this symbol."         | `POST /api/v3/order/cancelReplace` or `order.cancelReplace` (WebSocket API) is not supported for this symbol.
"This symbol is not permitted for this account."                 | The account and trading pair permissions do not match (e.g., `SPOT`, `MARGIN`, etc.).
"This symbol is restricted for this account."                    | The account does not have permission to trade on this symbol (e.g., if the account only has `ISOLATED_MARGIN` permission, it cannot place `SPOT` orders).
"Order was not canceled due to cancel restrictions."             | `cancelRestrictions` is set to `ONLY_NEW` but the order status is not `NEW` <br/> or <br/> `cancelRestrictions` is set to `ONLY_PARTIALLY_FILLED` but the order status is not `PARTIALLY_FILLED`.
"Rest API trading is not enabled." / "WebSocket API trading is not enabled." | When placing an order, the server is not configured to allow access to `TRADE` endpoints.
"FIX API trading is not enabled."                               | The order is placed on a FIX server that does not have TRADE enabled.
"Order book liquidity is less than `LOT_SIZE` filter minimum quantity." | When order book liquidity is less than the minimum quantity configured in the `LOT_SIZE` filter, market orders with `quoteOrderQty` cannot be submitted.
"Order book liquidity is less than `MARKET_LOT_SIZE` filter minimum quantity." | When order book liquidity is less than the minimum quantity of the `MARKET_LOT_SIZE` filter, market orders with `quoteOrderQty` cannot be submitted.
"Order book liquidity is less than symbol minimum quantity." | When there are no orders in the order book, market orders with `quoteOrderQty` cannot be submitted.
"Order amend (quantity increase) is not supported." | `newQty` must be less than the original order quantity (`quantity`).
"The requested action would change no state; rejecting". | The sent request would not change the current state; rejected.<br></br>(For example, `newQty` cannot be the same as the original order quantity (`quantity`).)
"Pegged orders are not supported for this symbol." | `pegInstructionsAllowed` has not been enabled. |
"This order type may not use pegged price." | The `pegPriceType` parameter is used on an unsupported order type (e.g., `MARKET`). |
"This price peg cannot be used with this order type." | Using `pegPriceType`=`MARKET_PEG` on a `LIMIT_MAKER` order. |
"Order book liquidity is too low for this pegged order." | There is no best price level available in the order book to peg the price. |
| OPO orders are not supported for this symbol. | OPO orders are not supported for this symbol. |
| Order amend (pending OPO order) is not supported. | The pending order in an OPO order cannot have its quantity amended. |

## Errors related to placing orders using cancelReplace

### -2021 Order cancel-replace partially failed
* This error code means the cancel **or** the new order placement failed.

### -2022 Order cancel-replace failed.
* This error code means both the cancel **and** the new order placement failed.

<a id="filter-failures"></a>

## Filter Failures
Error Message | Description
------------ | ------------
"Filter failure: PRICE_FILTER" | Check the price upper limit, lower limit, and tick size.
"Filter failure: PERCENT_PRICE" | Check whether the price in the order has moved more than X percent relative to the average price of the past N minutes.
"Filter failure: LOT_SIZE" | Check the upper limit, lower limit, and step size of the order quantity.
"Filter failure: MIN_NOTIONAL" | Check whether `price` * `quantity` (i.e., order amount) exceeds the minimum value.
"Filter failure: NOTIONAL" | `price` * `quantity` is not within the range of `minNotional` and `maxNotional`.
"Filter failure: ICEBERG_PARTS" | An iceberg order can only be split into a limited number of smaller orders.
"Filter failure: MARKET_LOT_SIZE" | Same meaning as `LOT_SIZE`, but applies to market orders.
"Filter failure: MAX_POSITION" | The account's position has reached the defined maximum limit.<br/> It consists of the sum of the base asset balance and the sum of all open buy order quantities.
"Filter failure: MAX_NUM_ORDERS" | The maximum number of open orders for the account on this trading pair.
"Filter failure: MAX_NUM_ALGO_ORDERS" | The maximum number of open take profit/stop loss orders for the account on this trading pair.
"Filter failure: MAX_NUM_ICEBERG_ORDERS" | The maximum number of iceberg orders for the account on this trading pair.
"Filter failure: MAX_NUM_ORDER_AMENDS" | The maximum number of amendments for a single order for the account on this trading pair.
"Filter failure: MAX_NUM_ORDER_LISTS" | The maximum number of order lists for the account on this trading pair.
"Filter failure: TRAILING_DELTA" | `trailingDelta` is not within the defined range of the filter for this order type.
"Filter failure: EXCHANGE_MAX_NUM_ORDERS" | The account has too many open orders on the exchange.
"Filter failure: EXCHANGE_MAX_NUM_ALGO_ORDERS" | The account has too many open stop loss and/or take profit orders on the exchange.
"Filter failure: EXCHANGE_MAX_NUM_ICEBERG_ORDERS" | The account has too many open iceberg orders on the exchange.
"Filter failure: EXCHANGE_MAX_NUM_ORDER_LISTS" | The account has too many open order lists on the exchange.
