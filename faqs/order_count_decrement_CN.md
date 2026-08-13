# Spot Unfilled Order Count Rules

To ensure a fair and orderly spot market, we limit the rate at which new orders can be placed.

The rate limit applies to the number of new, *unfilled* orders placed within a time interval. That is, orders that are partially or fully filled do not count toward the rate limit.

> [!NOTE]
> The unfilled order rate limit rewards efficient traders.
> As long as your orders are filled, you can continue trading.
> Details: [How do filled orders affect the rate limit?](#filled-orders-rate-limit)

### What is the current rate limit?

You can query the current rate limit using the "exchange information" request.

`"rateLimitType": "ORDERS"` indicates the current unfilled order rate limit.

Please refer to the API documentation:

| API | Request |
| :---- | :---- |
| FIX API | [LimitQuery`<XLQ>`](../fix-api_CN.md#limitquery) |
| REST API | [`GET /api/v3/exchangeInfo`](../rest-api_CN.md#exchangeInfo) |
| WebSocket API | [`exchangeInfo`](../web-socket-api_CN.md#exchangeInfo) |

> [!IMPORTANT]
> Order placement requests are also subject to the general request rate limits on the REST and WebSocket APIs, and the message limits on the FIX API.
> If you send too many requests at a high rate, you will be blocked by the API.

<a id=order-rate-limit></a>

### How does the unfilled `ORDERS` rate limit work?

Each successful order placement request increments the unfilled order count for the current time interval. If too many unfilled orders accumulate within the interval, subsequent requests will be rejected.

For example, if the unfilled order rate limit is 100 per 10 seconds:

```javascript
{
    "rateLimitType": "ORDERS",
    "interval": "SECOND",
    "intervalNum": 10,
    "limit": 100
}
```

Then you can place up to 100 new orders between 12:34:00 and 12:34:10, then another 100 between 12:34:10 and 12:34:20, and so on.

> [!TIP]
>If a newly placed order is filled, your unfilled order count will decrease, allowing you to place more orders within that interval.
>Details: [How do filled orders affect the rate limit?](#filled-orders-rate-limit)

When an order is rejected by the system due to the unfilled order rate limit, the HTTP status code will be set to `429 Too Many Requests`, with error code `-1015 "Too many new orders"`.

If you encounter these errors, stop sending orders until the affected rate limit interval expires.

Please refer to the API documentation:

| API | Documentation |
| ---- | ---- |
| FIX API | [Unfilled Order Count](../fix-api_CN.md#unfilled-order-count) |
| REST API | [Unfilled Order Count](../rest-api_CN.md#unfilled-order-count) |
| WebSocket API | [Unfilled Order Count](../web-socket-api_CN.md#unfilled-order-count) |

### Is the unfilled order count tracked per IP address?

The unfilled order count is tracked per **(sub-)account**.

The unfilled order count is shared across all IP addresses, all API keys, and all APIs.

<a id="filled-orders-rate-limit"></a>

### How do filled orders affect the unfilled order count?

When an order is first (partially or fully) filled, your unfilled order count will decrease by one across all time intervals of the `ORDERS` rate limit. In effect, filled orders do not count toward the rate limit, allowing efficient traders to continue placing new orders.

Certain orders provide additional incentives:

* **Orders that do not immediately fill (i.e., first filled as a maker).**
* Orders filled in large quantities.

In these cases, for each order that begins trading, the unfilled order count may decrease by more than one.

**Note:**

* **These examples only provide a general concept of the behavior.** For simplicity, a 10-second interval is used. The actual configuration on the live trading platform may differ.
* There is a brief delay between an order being executed and the unfilled order count being updated. Please be careful when your unfilled order count is close to the limit.
* Please refer to [How does the unfilled `ORDERS` rate limit work?](#order-rate-limit) for information on how to monitor the unfilled order count via the API.

**Example 1** — taker:

| Time | Action | Unfilled Order Count |
| :---- | :---- | :---- |
| 00:00:00 |  | 0 |
| 00:00:01 | Place order A (LIMIT)  | 1 — new order (+1) |
| 00:00:02 | Place order B (LIMIT) | 2 — new order (+1) |
|  | (order B partially filled) | 1 — first fill as taker (−1) |
| 00:00:03 | Place order C (LIMIT) | 2 — new order (+1) |
| 00:00:04 | (order B partially filled) | 2 |
| 00:00:04 | (order B fully filled) | 2 |
| 00:00:05 | Place order D (MARKET) | 3 — new order (+1) |
|  | (order D fully filled) | 2 — first fill as taker (−1) |

Note that for each taker order that trades immediately, the unfilled order count decreases shortly after, allowing you to continue placing orders.

**Example 2** — maker:

| Time | Action | Unfilled Order Count |
| :---- | :---- | :---- |
| 00:00:00 |  | 0 |
| 00:00:01 | Place order A (LIMIT)  | 1 — new order (+1) |
| 00:00:01 | Place order B (LIMIT)  | 2 — new order (+1) |
| 00:00:02 | Place order C (LIMIT)  | 3 — new order (+1) |
| 00:00:02 | Place order D (LIMIT) | 4 — new order (+1) |
| 00:00:02 | Place order E (LIMIT) | 5 — new order (+1) |
| 00:00:03 | (order A partially filled) | 0 — first fill as maker (−5) |
| 00:00:04 | Place order F (LIMIT) | 1 — new order (+1) |
| 00:00:04 | Place order G (LIMIT) | 2 — new order (+1) |
| 00:00:05 | (order A partially filled) | 2 |
| 00:00:05 | (order A fully filled) | 2 |
| 00:00:05 | (order B partially filled) | 0 — first fill as maker (−5) |
| 00:00:06 | Place order H (LIMIT) | 1 — new order (+1) |

Note that for each maker order executed later, the unfilled order count decreases by a larger amount, allowing you to place more orders.

### How do cancelled or expired orders affect the unfilled order count?

Cancelling an order does not change the unfilled order count.

Expired orders also do not change the unfilled order count.

**Example:**

| Time | Action | Unfilled Order Count |
| :---- | :---- | :---- |
| 00:00:00 |  | 0 |
| 00:00:01 | Place order A (LIMIT) | 1 — new order (+1) |
| 00:00:02 | Cancel order A | 1 |
| 00:00:02 | Place order B (LIMIT) | 2 — new order (+1) |
| 00:00:03 | Place order C (LIMIT FOK) | 3 — new order (+1) |
|  | (order C filled) | 2 — fill (−1) |
| 00:00:05 | Place order D (LIMIT) | 3 — new order (+1) |
| 00:00:06 | Place order E (LIMIT FOK) | 4 — new order (+1) |
|  | (order E expired without fill) | 4 |
| 00:00:07 | Cancel order D | 4 |
| 00:00:07 | Place order F (LIMIT) | 5 — new order (+1) |

### What timezone does `interval: DAY` use?

UTC.

### What happens if I placed an order yesterday but it fills the next day?

Regardless of when the order was filled, a newly filled order will reduce your current unfilled order count.

**Example:**

| Time | Action | Unfilled Order Count |
| :---- | :---- | :---- |
| 2024-01-01 09:00 | Place 5 orders: 1..5 | 5 |
| 2024-01-02 00:00 | (rate interval reset) | 0 |
| 2024-01-02 09:00 | Place 10 orders: 6..15 | 10 |
| 2024-01-02 12:00 | (orders 1..5 filled) | 5 |
| 2024-01-02 13:00 | (orders 6..10 filled) | 0 |
| 2024-01-02 14:00 | Place 2 orders: 16, 17 | 2 |
| 2024-01-02 15:00 | (orders 11..15 filled) | 0 |

**Note:** You do not receive credit for order fills. That is, once the unfilled order count drops to zero, additional fills will not further affect the count. New orders will still increment the count as usual.
