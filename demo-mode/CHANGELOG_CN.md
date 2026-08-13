# Axvn SPOT Demo Trading Changelog

**Last updated: 2026-07-01**

### 2026-07-01

* Planned downtime maintenance will begin on **2026-07-03 07:00 UTC**, expected to last approximately 4 hours.
* This will include upgrades to our infrastructure.

---

### 2026-05-11

**Note: The following changes will occur around 2026-05-12 07:00 (UTC)**.

* Added WebSocket market data stream support for [Block Trades](https://www.axvn.vn/en/support/faq/detail/557f95eaf8fb4460aed0a891d42a1425).
  * New Stream:
    * `<symbol>@blockTrade`

---

### 2026-03-12

**Notice:** FIX TLS connection update will take place on **2026-06-08**, starting from **03:00 UTC**, expected to take approximately 1 hour to complete.

**Please take action promptly:**

* During the update, existing FIX connections may intermittently disconnect. To ensure successful reconnection and new connections after the update, please confirm before our update that your client sends SNI (Server Name Indication) during the TLS handshake and validates certificates against the target hostname.
* Clients not sending SNI may receive certificate error messages during or after the update, causing TLS handshake or hostname validation failures. This may occur on certain Node.js clients not configured with SNI.
* Please refer to the [FIX API documentation](../fix-api_CN.md#general-api-information) for full details.

---

### 2026-03-09

Planned downtime maintenance will begin on **2026-03-13 06:00 UTC**, expected to last approximately 4 hours.

---

### 2026-01-29

This changelog will announce all planned maintenance downtime for the SPOT Demo Trading environment.

For more information on how to use Demo Trading via API, please see the [SPOT Demo Trading](general-info_CN.md) page.
