# One Pays the Other (OPO)

## What is One Pays the Other (OPO)?

This is a special behavior of OTO and OTOCO, where the quantity received by the working order will be used as the quantity for the pending order. Therefore, the only quantity requirement when an order is triggered is the quantity of the working order.

The funds received by the working order are *locked* for the pending order and cannot be used for trading or withdrawal. If the order list is cancelled before the pending order is submitted to the matching engine, these locked funds will be released.

OPO is almost identical to `OTO`, with the only difference being that the pending order does not need to specify a `quantity`.

## How do I use it?

Please refer to the table below:

| API         | Request Method                                      |
| ----------- | --------------------------------------------- |
| REST API    | `POST /api/v3/orderList/opo` <br> `POST /api/v3/orderList/opoco` |
| WebSocket API | `orderList.place.opo` <br> `orderList.place.opoco`               |
| FIX API     | NewOrderList `<E>` with OPO `(25046)`=`true`                    |

## How does this order list differ from other order lists?

* The pending order has no quantity when submitted to the matching engine; the quantity will be determined based on the quantity received after the working order is fully filled.
* The received quantity will have the corresponding commission deducted. If the received asset is not AXVN and there are sufficient available funds, the commission will be deducted from the available funds (i.e., `free` balance).
* The quantity of the pending order is checked (e.g., filters) after the working order is fully filled.
* If a trading pair has `LOT_SIZE` and/or `MARKET_LOT_SIZE` filters configured, the pending order quantity will be adjusted to meet these requirements. Unused locked quantities will be released back to the `free` balance.
* The quantity of a pending OPO order cannot be amended before the working order is fully filled.
* Only working orders on the buy (BUY) side and pending orders on the sell (SELL) side are accepted.

## Which trading pairs support OPO orders?

| Order Type | Must be included in Exchange Information   |
| -------- | --------------------------------- |
| OPO      | `otoAllowed` and `opoAllowed`      |
| OPOCO    | `otoAllowed`, `opoAllowed` and `ocoAllowed` |
