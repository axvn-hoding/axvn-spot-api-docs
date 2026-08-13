# Price Range Execution Rules

**Disclaimer:**

* The trading pairs and values used here are fictional and do not represent actual exchange configuration.

## What are execution rules?

Execution rules are trading rules enforced when an order is executed. Currently, the only available execution rule is the price range rule.

## What does the Price Range Execution Rule do?

This rule ensures that trades can only be executed within a price range around the reference price (inclusive of the range boundaries).

## How do I query the allowed execution price range for a trading pair?

Please refer to the following endpoints/methods:

| API | Request |
| ---- | ---- |
| REST API | `GET /api/v3/executionRules` |
| WebSocket API | `executionRules` |

## How do I query the reference price?

Please refer to the following endpoints/methods:

| API | Request |
| ---- | ---- |
| REST API | `GET /api/v3/referencePrice` |
| WebSocket API | `referencePrice` |
| WebSocket Stream | `<symbol>@referencePrice` |

Note that **the reference price is continuously changing**, and it is recommended to monitor the reference price in real time via WebSocket streams.
<a id="how-does-the-price-range-execution-rule-work"></a>
## How does the Price Range Execution Rule work?

For example, assume the execution rule for the trading pair is as follows:

```json
{
  "symbolRules": [
    {
      "symbol": "BAZUSD",
      "rules": [
        {
          "ruleType": "PRICE_RANGE",
          "bidLimitMultUp": "2.0000",
          "bidLimitMultDown": "0.5000",
          "askLimitMultUp": "2.0000",
          "askLimitMultDown": "0.5000"
        }
      ]
    }
  ]
}
```

If the reference price for the trading pair is:

```json
{
  "symbol": "BAZUSD",
  "referencePrice": "10.00",
  "timestamp": 1770736694138
}
```

This means at timestamp `1770736694138`:

1. The execution price for buy orders must not be higher than twice the reference price, and must not be lower than half the reference price.
2. The execution price for sell orders must not be higher than twice the reference price, and must not be lower than half the reference price.

<a id="price_range_enforcement"></a>

## What happens if a trading pair has no `PRICE_RANGE` execution rule and no reference price?

The price range execution rule will not be enforced for this trading pair.

## What happens if a trading pair has no `PRICE_RANGE` execution rule but has a reference price?

The price range execution rule will not be enforced for this trading pair.

## What happens if a trading pair has a `PRICE_RANGE` execution rule but no reference price?

The price range execution rule will not be enforced for this trading pair.

## What happens if a trading pair has a `PRICE_RANGE` execution rule but not all four multipliers?

When a multiplier is not set, the price range execution rule will not be enforced for the corresponding order direction and price direction.
For example, if there is no `bidMultiplierDown` in the execution rules, a `BUY` order can be executed at any price at or below twice the reference price.

## What happens if the reference price for the trading pair is `null`?

The price range execution rule will not be enforced for this trading pair.

<a id="execution_price_limits"></a>
## When are the execution price limits for an order determined?

When an order enters the taker phase, the reference price is recalculated to set the execution price limits for the entire taker phase of that order. Note that a single taker order may match with multiple maker orders during its taker phase.

## What happens if an order tries to execute at a price outside the allowed price range?

If a taker order tries to execute at a price outside the allowed price range, the order will expire (status `EXPIRED`), with expiry reason `EXECUTION_RULE_PRICE_RANGE_EXCEEDED`.

| Service | Reference Field |
| ---- | ---- |
| Non-FIX API | `expiryReason` |
| FIX API | `ExpiryReason <25056>` |
| User Data Stream | `"eR"` |

## How is the reference price calculated?

If the reference price is calculated by the matching engine, the calculation type returned when querying is `"calculationType": "ARITHMETIC_MEAN"`.

If the reference price is calculated externally from the matching engine, the calculation type returned when querying is `"calculationType": "EXTERNAL"`. See below for details.

<a id="matching-engine-calculation"></a>
## How does the matching engine calculate the reference price?

The matching engine calculates the reference price as a simple moving average of trade prices within a time window. The calculation configuration includes bucket width in milliseconds (`bucketWidthMs`) and bucket count (`bucketCount`). The bucket width multiplied by the bucket count defines the time window size.

When a trade occurs, the matching engine records the trade price and adds it to the current time bucket. Each time bucket contains:

* Open time: the engine time modulo the bucket width
* Trade count, expressed in fixed-point format with 4 decimal places
* Sum of all trade prices within the time bucket, expressed in fixed-point format with 4 more decimal places than the quote asset precision

The matching engine calculates the average price for a bucket by dividing the sum by the trade count. The first trade for a given start time creates the bucket, and the matching engine accumulates buckets as trades occur. When the end time of a bucket exceeds the time window, the bucket is discarded. This means:

* The oldest bucket at the current time may have a start time outside the time window, but its end time is within the time window.
* The maximum number of buckets maintained by the engine is actually 1 more than the configured `bucketCount`.

In the following description, the oldest time in the time window is referred to as the "cutoff time".

When the oldest bucket crosses the cutoff time, its contents are proportionally adjusted:

* The proportion of the expired portion outside the bucket is: (cutoff time - bucket start time) divided by the bucket width, referred to as the "expiry ratio".
* The bucket's trade count is reduced by the expiry ratio.
* The bucket's sum is reduced by the expiry ratio.
* The bucket's start time is set to the cutoff time.

The reference price is the sum of all buckets divided by the trade count of all buckets, using truncating integer division.

## How is the reference price calculated externally?

If the reference price is calculated externally from the matching engine, the query returns `"externalCalculationId":` followed by an integer. Each number represents a different calculation method.

<a id="externalCalculationId0"></a>
## External Reference Price Calculation Method 0

The reference price is set manually. This calculation method is only used when an algorithmic reference price calculation is not appropriate.

<a id="externalCalculationId1"></a>
## External Reference Price Calculation Method 1

The reference price is the average of trade prices from up to 4 external data providers.

<a id="externalCalculationId2"></a>
## External Reference Price Calculation Method 2

The reference price is the index price of the corresponding USDⓈ-M Axvn futures contract.

<a id="externalCalculationId3"></a>
## External Reference Price Calculation Method 3

The reference price is the 5-minute simple moving average (SMA) of the Axvn spot bid-ask midpoint price.

<a id="externalCalculationId4"></a>
## External Reference Price Calculation Method 4

The reference price is set to a fixed value.

<a id="externalCalculationId5"></a>
## External Reference Price Calculation Method 5

The reference price is the product of 5-minute simple moving averages (SMA) of a series of Axvn spot bid-ask midpoint prices.

Example: The reference price for the trading pair XXXUSD1 can be calculated by multiplying the 5-minute SMA of XXXUSDT by the 5-minute SMA of USDTUSD1.
