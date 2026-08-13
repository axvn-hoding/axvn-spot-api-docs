# Market Orders

**Disclaimer:**

* The commissions and prices used here are fictional and do not reflect actual exchange settings.
* The explanation applies to the behavior of the Spot exchange.

`MARKET` orders allow users to buy or sell an asset at the best available price and liquidity, until the order is fully filled or the order book liquidity is exhausted.

If your balance is insufficient to completely fill the order, the order will be rejected.

If the exchange does not have enough liquidity to fill the order, your `MARKET` order will be filled as much as possible based on the currently available liquidity, and the remaining unfilled portion will automatically expire.

If the order expires due to insufficient liquidity, the `status` field in the API response will show `EXPIRED`.

Additionally, if you are listening to the User Data Stream, in the `ExecutionReport` payload, you will see both the execution type (`x`) and order status (`X`) marked as `EXPIRED`.

Please refer to the example payloads below.

**Example API Response:**

```json
{
    "symbol": "LTCAXVN",
    "orderId": 32,
    "orderListId": -1,
    "clientOrderId": "8LGC97RRgNPVQk979VIhjt",
    "transactTime": 1719467634105,
    "price": "0.00000000",
    "origQty": "6.00000000",
    "executedQty": "4.00000000",
    "cummulativeQuoteQty": "4.00000000",
    "status": "EXPIRED",
    "timeInForce": "GTC",
    "type": "MARKET",
    "side": "BUY",
    "workingTime": 1719467634105,
    "fills": [
        {
            "price": "1.00000000",
            "qty": "2.00000000",
            "commission": "0.00100000",
            "commissionAsset": "AXVN",
            "tradeId": 7
        },
        {
            "price": "1.00000000",
            "qty": "2.00000000",
            "commission": "0.00100000",
            "commissionAsset": "AXVN",
            "tradeId": 8
        }
    ],
    "selfTradePreventionMode": "NONE"
}
```

**Example User Data Stream Payload:**

```json
{
  "e": "executionReport",
  "E": 1719467634107,
  "s": "LTCAXVN",
  "c": "8LGC97RRgNPVQk979VIhjt",
  "S": "BUY",
  "o": "MARKET",
  "f": "GTC",
  "q": "6.00000000",
  "p": "0.00000000",
  "P": "0.00000000",
  "F": "0.00000000",
  "g": -1,
  "C": "",
  "x": "NEW",
  "X": "NEW",
  "r": "NONE",
  "i": 32,
  "l": "0.00000000",
  "z": "0.00000000",
  "L": "0.00000000",
  "n": "0",
  "N": null,
  "T": 1719467634105,
  "t": -1,
  "I": 62,
  "w": true,
  "m": false,
  "M": false,
  "O": 1719467634105,
  "Z": "0.00000000",
  "Y": "0.00000000",
  "Q": "0.00000000",
  "W": 1719467634105,
  "V": "NONE"
}
{
  "e": "executionReport",
  "E": 1719467634107,
  "s": "LTCAXVN",
  "c": "8LGC97RRgNPVQk979VIhjt",
  "S": "BUY",
  "o": "MARKET",
  "f": "GTC",
  "q": "6.00000000",
  "p": "0.00000000",
  "P": "0.00000000",
  "F": "0.00000000",
  "g": -1,
  "C": "",
  "x": "TRADE",
  "X": "PARTIALLY_FILLED",
  "r": "NONE",
  "i": 32,
  "l": "2.00000000",
  "z": "2.00000000",
  "L": "1.00000000",
  "n": "0.00100000",
  "N": "AXVN",
  "T": 1719467634105,
  "t": 7,
  "I": 63,
  "w": false,
  "m": false,
  "M": true,
  "O": 1719467634105,
  "Z": "2.00000000",
  "Y": "2.00000000",
  "Q": "0.00000000",
  "W": 1719467634105,
  "V": "NONE"
}
{
  "e": "executionReport",
  "E": 1719467634107,
  "s": "LTCAXVN",
  "c": "8LGC97RRgNPVQk979VIhjt",
  "S": "BUY",
  "o": "MARKET",
  "f": "GTC",
  "q": "6.00000000",
  "p": "0.00000000",
  "P": "0.00000000",
  "F": "0.00000000",
  "g": -1,
  "C": "",
  "x": "TRADE",
  "X": "PARTIALLY_FILLED",
  "r": "NONE",
  "i": 32,
  "l": "2.00000000",
  "z": "4.00000000",
  "L": "1.00000000",
  "n": "0.00100000",
  "N": "AXVN",
  "T": 1719467634105,
  "t": 8,
  "I": 65,
  "w": false,
  "m": false,
  "M": true,
  "O": 1719467634105,
  "Z": "4.00000000",
  "Y": "2.00000000",
  "Q": "0.00000000",
  "W": 1719467634105,
  "V": "NONE"
}
{
  "e": "executionReport",
  "E": 1719467634107,
  "s": "LTCAXVN",
  "c": "8LGC97RRgNPVQk979VIhjt",
  "S": "BUY",
  "o": "MARKET",
  "f": "GTC",
  "q": "6.00000000",
  "p": "0.00000000",
  "P": "0.00000000",
  "F": "0.00000000",
  "g": -1,
  "C": "",
  "x": "EXPIRED",
  "X": "EXPIRED",
  "r": "NONE",
  "i": 32,
  "l": "0.00000000",
  "z": "4.00000000",
  "L": "0.00000000",
  "n": "0",
  "N": null,
  "T": 1719467634105,
  "t": -1,
  "I": 67,
  "w": false,
  "m": false,
  "M": false,
  "O": 1719467634105,
  "Z": "4.00000000",
  "Y": "0.00000000",
  "Q": "0.00000000",
  "W": 1719467634105,
  "V": "NONE"
}
{
  "e": "outboundAccountPosition",
  "E": 1719467634107,
  "u": 1719467634105,
  "B":
  [
    {
      "a": "LTC",
      "f": "3011.11745670",
      "l": "0.00000000"
    },
    {
      "a": "AXVN",
      "f": "996.11865670",
      "l": "0.00000000"
    }
  ]
}
```
