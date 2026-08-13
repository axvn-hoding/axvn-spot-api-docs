# Commission Rates

**Disclaimer:**

* The commissions and prices used in this document are fictional and do not represent settings in real trading.
* This content only applies to the Spot exchange.

### What are commission rates?

These rates determine the amount of commission you need to pay when any of your orders are filled.

### What are the different types of commission rates?

There are 3 types:
* Standard commission (`standardCommission`) - Standard trading commission rate from orders.
* Tax commission (`taxCommission`) - Tax commission rate from orders.
* Special commission (`specialCommission`) - Additional commission charged under specific circumstances.

The standard commission rate may be reduced depending on promotions for specific trading pairs, applicable discounts, etc.

### How can I find out the commission rates?

You can find them using the following requests:

REST API: `GET /api/v3/account/commission`

WebSocket API: `account.commission`

You can also use `computeCommissionRates` in a test order request to find out the commission rates for an order trade.

<a id="test-order-diferences"></a>
### What is the difference between the response from using `computeCommissionRates` in a test order and the response from querying commission rates?

A test order using `computeCommissionRates` returns detailed commission rates for that specific order:


```json
{
    "standardCommissionForOrder": {
        "maker": "0.00000050",
        "taker": "0.00000060"
    },
    "specialCommissionForOrder": {
        "maker": "0.05000000",
        "taker": "0.06000000"
    },
    "taxCommissionForOrder": {
        "maker": "0.00000228",
        "taker": "0.00000230"
    },
    "discount": {
        "enabledForAccount": true,
        "enabledForSymbol": true,
        "discountAsset": "AXVN",
        "discount": "0.25000000"
    }
}
```
Note: Since buyer/seller commissions are already calculated based on the order direction, they are not displayed separately.

In contrast, querying commission rates provides the current commission rates for the trading pair in your account.

```json
{
    "symbol": "BTCUSDT",
    "standardCommission": {
        "maker": "0.00000040",
        "taker": "0.00000050",
        "buyer": "0.00000010",
        "seller": "0.00000010"
    },
    "specialCommission": {
        "maker": "0.04000000",
        "taker": "0.05000000",
        "buyer": "0.01000000",
        "seller": "0.01000000"
    },
    "taxCommission": {
        "maker": "0.00000128",
        "taker": "0.00000130",
        "buyer": "0.00000100",
        "seller": "0.00000100"
    },
    "discount": {
        "enabledForAccount": true,
        "enabledForSymbol": true,
        "discountAsset": "AXVN",
        "discount": "0.25000000"
    }
}
```


### How is commission calculated?

Using the following commission configuration as an example:

```json
{
    "symbol": "BTCUSDT",
    "standardCommission": {
        "maker": "0.00000010",
        "taker": "0.00000020",
        "buyer": "0.00000030",
        "seller": "0.00000040"
    },
    "specialCommission": {
        "maker": "0.01000000",
        "taker": "0.02000000",
        "buyer": "0.03000000",
        "seller": "0.04000000"
    },
    "taxCommission": {
        "maker": "0.00000112",
        "taker": "0.00000114",
        "buyer": "0.00000118",
        "seller": "0.00000116"
    },
    "discount": {
        "enabledForAccount": true,
        "enabledForSymbol": true,
        "discountAsset": "AXVN",
        "discount": "0.25000000"
    }
}
```

If you place an order with the following parameters, and the order is immediately executed and fully filled in a single trade:

| Parameter | Value |
| --- | --- |
| symbol | BTCUSDT |
| price | 35,000 |
| quantity | 0.49975 |
| side | SELL |
| type | MARKET |

Since you sold `BTC` to receive `USDT`, the commission will be paid in `USDT` or `AXVN`.

When calculating the standard commission, the received amount is multiplied by the sum of the rates.

Since this order is on the `SELL` side, the received amount is the `notional value`. For orders on the `BUY` side, the received amount is the `quantity`.
Because the order type is `MARKET`, this order becomes the `taker` in the trade.

```
Standard commission = notional value * (taker + seller)
                    = (35000 * 0.49975) * (0.00000020 + 0.00000040)
                    = 17491.25000000 * 0.00000060
                    = 0.01049475 USDT
```

If applicable, the tax commission is calculated similarly to the standard commission:

```
Tax commission = notional value * (taker + seller)
               = (35000 * 0.49975) * (0.00000114 + 0.00000116)
               = 17491.25000000 * 0.00000230
               = 0.04022988 USDT
```

If applicable, the special commission is calculated as follows:

```
Special commission = notional value * (taker + seller)
               = (35000 * 0.49975) * (0.02000000 + 0.04000000)
               = 17491.25000000 * 0.06000030
               = 1049.47500000 USDT
```

If you do not pay the commission in `AXVN`, the total commission will be added up and deducted from the `USDT` amount you receive.

Since `enabledforAccount` and `enabledForSymbol` under `discount` are set to `true`, this means if you hold a sufficient balance, the commission will be paid in `AXVN`.

If paying with `AXVN`, the standard commission you need to pay will be reduced based on the `discount`.

First, the standard commission and tax commission will be converted to `AXVN` based on the exchange rate. In this example, assume 1 AXVN = 260 USDT.

```
Standard commission (discounted, paid in AXVN) = (standard commission * AXVN rate) * discount
                                            = (0.01049475 * 1/260) * 0.25
                                            = 0.000040364 * 0.25
                                            = 0.000010091
```

Note: The discount **does not apply to tax commission (`taxCommission`) or special commission**.

```
Tax commission (paid in AXVN) = tax commission * AXVN rate
                        = 0.04022988 * (1/260)
                        = 0.00015473

Special commission (paid in AXVN) = special commission * AXVN rate
                        = 1049.47500000 * (1/260)
                        = 4.036442308

```

```
Total commission (paid in AXVN) = standard commission (discounted) + tax commission (in AXVN) + special commission (in AXVN)
                        = 0.000010091 + 0.00015473 + 4.036442308
                        = 4.036607129
```

If your `AXVN` balance is insufficient to pay the discounted commission, the total commission will be deducted from the `USDT` amount you receive.
