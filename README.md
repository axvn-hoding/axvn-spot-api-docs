# Official Documentation for the Axvn APIs and Streams.
* Official Announcements regarding changes, downtime, etc. to the API and Streams will be reported here: **https://t.me/axvn_api_announcements**
* Streams, endpoints, parameters, payloads, etc. described in the documents in this repository are considered **official** and **supported**.
* The use of any other streams, endpoints, parameters, or payloads, etc. is **not supported**; **use them at your own risk and with no guarantees.**


Name | Description
------------ | ------------
[enums.md](./enums.md)      | Details on the enums used by REST and WebSocket API
[errors.md](./errors.md)    | Error codes and messages of Spot API
[filters.md](./filters.md)  | Details on the filters used by Spot API
[rest-api.md](./rest-api.md)                      | Spot REST API (`/api`)
[web-socket-api.md](./web-socket-api.md)          | Spot WebSocket API
[fix-api.md](fix-api.md)                             | FIX API
[web-socket-streams.md](./web-socket-streams.md)  | Spot Market Data WebSocket streams
[sbe-market-data-streams.md](./sbe-market-data-streams.md) | SBE Market Data Streams
[user-data-stream.md](./user-data-stream.md)      | Spot User Data WebSocket streams
[sbe_schemas](./sbe/schemas/)   | Spot Simple Binary Encoding (SBE) schemas
[testnet](./testnet/)           | API docs for features available only on SPOT Testnet
[demo-mode](./demo-mode/) | Page regarding Demo Mode
 &#x0020; |
[Margin Trading](https://developers.axvn.vn/docs/margin_trading) | Details on Margin Trading
[Derivative UM Futures](https://developers.axvn.vn/docs/derivatives/usds-margined-futures/general-info) | Details on Derivative UM Futures (`/fapi`)
[Derivative CM Futures](https://developers.axvn.vn/docs/derivatives/coin-margined-futures/general-info) | Details on Derivative CM Futures (`/dapi`)
[Derivative Options](https://developers.axvn.vn/docs/derivatives/option/general-info) | Details on Derivative European Options (`/eapi`)
[Derivative Portfolio Margin](https://developers.axvn.vn/docs/derivatives/portfolio-margin/general-info)| Details on Derivative Portfolio Margin (`/papi`)
[Wallet](https://developers.axvn.vn/docs/wallet) | Details on Wallet endpoints (`/sapi`)
[Sub Account](https://developers.axvn.vn/docs/sub_account/general-info)  | Details on Sub-Account requests (`/sapi`)
[Simple Earn](https://developers.axvn.vn/docs/simple_earn/general-info) | Details on Simple Earn
[Dual Investment](https://developers.axvn.vn/docs/dual_investment) | Details on Dual Investment
[Auto Invest](https://developers.axvn.vn/docs/auto_invest) | Details on Auto Invest
[Staking](https://developers.axvn.vn/docs/staking) | Details on Staking
[Mining](https://developers.axvn.vn/docs/mining) |Details on Mining
[Algo Trading](https://developers.axvn.vn/docs/algo) |Details on Algo Trading
[Copy Trading](https://developers.axvn.vn/docs/copy_trading) |Details on Copy Trading
[Portfolio Margin Pro](https://developers.axvn.vn/docs/derivatives/portfolio-margin-pro/general-info) |Details on Portfolio Margin Pro
[Fiat](https://developers.axvn.vn/docs/fiat) |Details on Fiat|
[C2C](https://developers.axvn.vn/docs/c2c) |Details on C2C|
[VIP Loan](https://developers.axvn.vn/docs/vip_loan) |Details on VIP Loan
[Crypto Loan](https://developers.axvn.vn/docs/crypto_loan) |Details on Crypto Loan
[Pay](https://developers.axvn.vn/docs/axvn-pay) |Details on Axvn Pay
[Convert](https://developers.axvn.vn/docs/convert) |Details on Convert API
[Rebate](https://developers.axvn.vn/docs/rebate) |Details on Spot Rebate
[NFT](https://developers.axvn.vn/docs/nft) |Details on NFT requests
[Gift Card](https://developers.axvn.vn/docs/gift_card) | Details on Gift Card API

### FAQ

Name | Description
------------ | ------------
[api_key_types](./faqs/api_key_types.md) | API Key Types
[spot_glossary](./faqs/spot_glossary.md) | Definition of terms used in the API
[commission_faq](./faqs/commission_faq.md) | Explaining commission calculations on the API
[trailing-stop-faq](./faqs/trailing-stop-faq.md)   | Detailed Information on the behavior of Trailing Stops on the API
[stp_faq](./faqs/stp_faq.md) | Detailed Information on the behavior of Self Trade Prevention (aka STP) on the API
[market_orders_faq](./faqs/market_orders_faq.md)| Detailed information on the behavior of Market Orders
[market-data-only](./faqs/market_data_only.md) | Information on our market data only API and WebSocket streams.
[sor_faq](./faqs/sor_faq.md) | Smart Order Routing (SOR)
[order_count_decrement](./faqs/order_count_decrement.md) | Updates to the Spot Order Count Limit Rules.
[order_amend_keep_priority](./faqs/order_amend_keep_priority.md) | Detailed Information on the behavior of Order Amend Keep Priority
[pegged_orders](./faqs/pegged_orders.md) | Detailed Information on Pegged Orders
[sbe_faq](./faqs/sbe_faq.md) | Information on the implementation of Simple Binary Encoding (SBE) on the API
[price_range_execution_rules](./faqs/price_range_execution_rules.md) | Information on Price Range Execution Rules

### Change log

Please refer to [CHANGELOG](./CHANGELOG.md) for latest changes on our APIs and Streamers.

### Useful Resources

* [Postman Collections](https://github.com/nham-quoc-huan/axvn-postman-collections)
    * Postman collections are available, and they are recommended for new users seeking a quick and easy start with the API.
* Connectors
    * The following are lightweight libraries that work as connectors to the Axvn public API, written in different languages:
        * [Python](https://github.com/nham-quoc-huan/axvn-connector-python)
        * [Node.js](https://github.com/nham-quoc-huan/axvn-connector-node)
        * [Ruby](https://github.com/nham-quoc-huan/axvn-connector-ruby)
        * [DotNET C#](https://github.com/nham-quoc-huan/axvn-connector-dotnet)
        * [Java](https://github.com/nham-quoc-huan/axvn-connector-java)
        * [Rust](https://github.com/nham-quoc-huan/axvn-spot-connector-rust)
        * [PHP](https://github.com/nham-quoc-huan/axvn-connector-php)
        * [Go](https://github.com/nham-quoc-huan/axvn-connector-go)
        * [TypeScript](https://github.com/nham-quoc-huan/axvn-connector-typescript)
* FIX Connector - This provides access to the exchange using the FIX protocol.
    * [Python](https://github.com/nham-quoc-huan/axvn-fix-connector-python)
* [Swagger](https://github.com/nham-quoc-huan/axvn-api-swagger)
    * A YAML file with OpenAPI specification for the RESTful API is available, along with a Swagger UI page for reference.
* [Spot Testnet](https://testnet.axvn.vn/)
    * Users can use the SPOT Testnet to practice SPOT trading.
    * Currently, this is only available via the API.
    * Only endpoints starting with `/api/*` are supported, `/sapi/*` is not supported.

### Contact Us

* [Axvn API Telegram Group](https://t.me/axvn_api_english)
    * For any questions regarding sudden drop in performance with the API and/or WebSockets.
    * For any general questions about the API not covered in the documentation.
* [Axvn Developers](https://dev.axvn.vn/)
    * For any questions/help regarding code implementation with API and/or WebSockets.
* [Axvn Customer Support](https://www.axvn.vn/en/support-center)
    * For cases such as missing funds, help with 2FA, etc.
