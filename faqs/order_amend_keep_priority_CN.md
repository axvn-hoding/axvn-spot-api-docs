# Order Amend Keep Priority FAQ

**Disclaimer:**

* The trading pairs and prices used here are fictional and do not reflect actual exchange settings.
* For simplicity, the examples in this document do not include commissions.

## What is Order Amend Keep Priority?

Order Amend Keep Priority is used to modify (amend) an existing order **without losing its priority on the order book**.

The following order modifications are allowed:

* Reducing the original quantity of an existing order.

## How do I amend my order quantity?

Use the following requests:

| API | Request |
| :---- | :---- |
| REST API | `PUT /api/v3/order/amend/keepPriority` |
| WebSocket API | `order.amend.keepPriority` |
| FIX API | OrderAmendKeepPriorityRequest `<XAK>` |

## What is the difference between "cancel-replace" and "Order Amend Keep Priority"?

**Cancel-replace** cancels the existing order and places a new one.<br> Time priority is lost. The new order executes after existing orders at the same price.

**Order Amend Keep Priority** modifies the existing order in place. <br>The amended order maintains its time priority among existing orders at the same price.

For example, consider the following order book:

| User | Order ID | Side | Price | Quantity |
| :---- | ----: | :---- | ----: | ----: |
| User A | 10 | BUY | 87,000 | 1.00 |
| ⭐️ You | 15 | BUY | 87,000 | 5.50 |
| User B | 20 | BUY | 87,000 | 4.00 |
| User C | 21 | BUY | 86,999 | 2.00 |

Your order 15 is the second order in the queue based on price and time.

You want to reduce the quantity from 5.50 by .50 to 5.00.

If you use **cancel-replace** to cancel `orderId=15` and place a new order with `qty=5.00`, the order book will look like this:

| User | Order ID | Side | Price | Quantity |
| :---- | ----: | :---- | ----: | ----: |
| User A | 10 | BUY | 87,000 | 1.00 |
| ~~⭐️ You~~ | ~~11~~ | ~~BUY~~ | ~~87,000~~ | ~~5.50~~ |
| User B | 20 | BUY | 87,000 | 4.00 |
| ⭐️ You | (new) 22 | BUY | 87,000 | 5.00 |
| User C | 21 | BUY | 86,999 | 2.00 |

Note: The new order gets a new order ID and you lose time priority: order 22 will trade after order 20.

If you instead use **Order Amend Keep Priority** to reduce `orderId=15` to `qty=5.00`, the order book will look like this:

| User | Order ID | Side | Price | Quantity |
| :---- | ----: | :---- | ----: | ----: |
| User A | 10 | BUY | 87,000 | 1.00 |
| ⭐️ You | 15 | BUY | 87,000 | (amended) **5.00** |
| User B | 20 | BUY | 87,000 | 4.00 |
| User C | 21 | BUY | 86,999 | 2.00 |

Note: The order ID remains the same and the order maintains its priority in the queue. Only the order quantity changes.

## Does Order Amend Keep Priority affect the unfilled order count (rate limit)?

Currently, Order Amend Keep Priority counts as 0 towards the unfilled order count.

## How can I tell if my order has been amended?

If the order amendment is successful, the API response will contain the order with the new quantity.

On the User Data Stream, you will receive an `"executionReport"` event with execution type `"x": "REPLACED"`.

If the amended order belongs to an order list and the client order ID has been changed, you will also receive a "listStatus" event with list status type `"l": "UPDATED"`.

You can also query the order amendment history using the following requests:

| API | Request |
| :---- | :---- |
| REST API | `GET /api/v3/order/amendments` |
| WebSocket API | `order.amendments` |

## What should I do if my amendment request is unsuccessful?

If the request fails for any reason (e.g., due to filters, permissions, account restrictions, etc.), the order amendment request will be rejected and the order will remain unchanged.

## Can I reuse the current clientOrderId in my amendment request?

Yes.

By default, the amended order receives a randomly generated new client order ID. However, you can reuse the existing current client order ID by assigning it to the `newClientOrderId` parameter.

## Can I amend iceberg orders?

Yes.

Note: The visible quantity of an iceberg order can only be changed when `newQty` is less than the unamended visible quantity.

## Can I amend orders in an order list?

Orders within an order list can be amended.

Note: OCO order pairs must have the same quantity since only one order can execute. This means amending either order will affect both orders.

For OTO orders, you can amend the working order and the pending order separately.

## Which trading pairs allow Order Amend Keep Priority?

This information can be found in Exchange Information.
Trading pairs that allow Order Amend Keep Priority will have `amendAllowed` set to `true`.
