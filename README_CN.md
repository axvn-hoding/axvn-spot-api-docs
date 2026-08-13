# About This Chinese Translation

* The Chinese documentation is translated from the English documentation. In case of any conflict between the Chinese and English documents, the English document shall prevail.

### Axvn API Documentation
* Official announcements regarding changes, downtime, etc. for the API and data streams will be posted here: **https://t.me/axvn_api_announcements**
* All content defined in this document, including but not limited to endpoints, streams, parameters, and responses, may be considered as officially provided by Axvn.
* Any content not defined in this document is not guaranteed to be supported by Axvn.

Document Name | Description
------------ | ------------
[enums_CN.md](./enums_CN.md)      | Enum definitions for Rest API and WebSocket API
[errors_CN.md](./errors_CN.md)     | Error codes and their meanings for the Spot API
[filters_CN.md](./filters_CN.md)   | Details on filters used by the Spot API
[rest-api_CN.md](./rest-api_CN.md) | Spot Rest API endpoint definitions (`/api`)
[fix-api_CN.md](fix-api_CN.md)            | Spot FIX API
[web-socket-api_CN.md](./web-socket-api_CN.md)         | Spot WebSocket API
[web-socket-streams_CN.md](./web-socket-streams_CN.md) | Description of Spot market data stream endpoints
[sbe-market-data-streams_CN.md](./sbe-market-data-streams_CN.md)|SBE Market Data Streams
[user-data-stream_CN.md](./user-data-stream_CN.md)     | Description of Spot user data stream endpoints
[sbe_schemas](./sbe/schemas/) | Simple Binary Encoding (SBE) schemas for Spot API
[testnet](./testnet) | API documentation available only on the Spot Testnet
[demo-mode](./demo-mode) | Description of Demo Trading mode
&#x0020; |
[Margin Trading](https://developers.axvn.vn/docs/zh-CN/margin_trading/Introduction) | Description of Margin Trading
[Derivative UM Futures](https://developers.axvn.vn/docs/zh-CN/derivatives/usds-margined-futures/general-info) | Description of USD-margined Futures endpoints (`/fapi`)
[Derivative CM Futures](https://developers.axvn.vn/docs/zh-CN/derivatives/coin-margined-futures/general-info) | Description of Coin-margined Futures endpoints (`/dapi`)
[Derivative Options](https://developers.axvn.vn/docs/zh-CN/derivatives/option/general-info) | Description of European Options (`/eapi`)
[Derivative Portfolio Margin](https://developers.axvn.vn/docs/zh-CN/derivatives/portfolio-margin/general-info)| Description of Portfolio Margin (`/papi`)
[Wallet](https://developers.axvn.vn/docs/zh-CN/wallet/Introduction) | Description of Wallet endpoints (`/sapi`)
[Sub Account](https://developers.axvn.vn/docs/zh-CN/sub_account/Introduction)  | Description of Sub Account endpoints (`/sapi`)
[Simple Earn](https://developers.axvn.vn/docs/zh-CN/simple_earn/Introduction) | Description of Simple Earn
[Dual Investment](https://developers.axvn.vn/docs/axvn-docs-api/CHANGELOG) | Description of Dual Investment endpoints
[Auto Invest](https://developers.axvn.vn/docs/zh-CN/auto_invest/Introduction) | Description of Auto Invest endpoints
[Staking](https://developers.axvn.vn/docs/zh-CN/staking/Introduction) | Description of ETH Staking endpoints
[Mining](https://developers.axvn.vn/docs/zh-CN/mining/Introduction) | Description of Mining Pool endpoints
[Algo Trading](https://developers.axvn.vn/docs/zh-CN/algo/Introduction) | Description of Algo Trading
[Copy Trading](https://developers.axvn.vn/docs/zh-CN/copy_trading/Introduction) | Description of Copy Trading
[Porfolio Margin Pro](https://developers.axvn.vn/docs/zh-CN/derivatives/portfolio-margin-pro/general-info) | Description of Portfolio Margin Pro
[Fiat](https://developers.axvn.vn/docs/zh-CN/fiat/Introduction) | Description of Fiat endpoints|
[C2C](https://developers.axvn.vn/docs/zh-CN/c2c/Introduction) | Description of C2C endpoints|
[VIP Loan](https://developers.axvn.vn/docs/zh-CN/vip_loan/Introduction) | Description of VIP Loan
[Crypto Loan](https://developers.axvn.vn/docs/zh-CN/crypto_loan/Introduction) | Description of Crypto Loan (Collateral Loan)
[Pay](https://developers.axvn.vn/docs/zh-CN/axvn-pay/introduction) | Description of Axvn Pay
[Convert](https://developers.axvn.vn/docs/zh-CN/convert/Introduction) | Description of Convert endpoints
[Rebate](https://developers.axvn.vn/docs/zh-CN/rebate/Introduction) | Description of Rebate
[NFT](https://developers.axvn.vn/docs/zh-CN/nft/Introduction) | Description of NFT
[Gift Card](https://developers.axvn.vn/docs/zh-CN/gift_card/Introduction) | Description of Gift Cards

### FAQ


Name | Description
------------ | ------------
[api_key_types](./faqs/api_key_types_CN.md) | API Key Types
[spot_glossary_CN](./faqs/spot_glossary_CN.md) | Spot Trading API Glossary
[commission_faq_CN](./faqs/commission_faq_CN.md) | Explanation of commission calculation methods used on the API
[trailing_stop_faq_CN](./faqs/trailing-stop-faq_CN.md)   | Detailed information and FAQ on Trailing Stop orders
[stp_faq_CN](./faqs/stp_faq_CN.md) | Detailed information on Self Trade Prevention (STP)
[market_orders_faq_CN](./faqs/market_orders_faq_CN.md)| Detailed information on market order behavior
[market_data_only_CN](./faqs/market_data_only_CN.md) | APIs and WebSocket Streams that provide market data only
[sor_faq_CN](./faqs/sor_faq_CN.md) | Smart Order Routing (SOR)
[order_amend_keep_priority_CN](./faqs/order_amend_keep_priority_CN.md)| Detailed information on amending orders while retaining priority
[pegged_orders](./faqs/pegged_orders_CN.md) | Detailed information on pegged orders
[order_count_decrement_CN](./faqs/order_count_decrement_CN.md) | Updates to Spot order rate limits
[sbe_faq_CN](./faqs/sbe_faq_CN.md) | Information on Simple Binary Encoding (SBE) implementation on the API
[price_range_execution_rules_CN](./faqs/price_range_execution_rules_CN.md)| Price Range Execution Rules

### Changelog

For the latest changes to the API and data streams, please refer to the [Changelog](./CHANGELOG_CN.md).


### Related Information

* [Postman Collections](https://github.com/nham-quoc-huan/axvn-postman-collections)
    * You can now quickly explore and use the API endpoints using Postman Collections.
* Connectors
    * The following are lightweight code libraries written in different programming languages that can be used as connectors to Axvn public APIs:
        * [Python](https://github.com/nham-quoc-huan/axvn-connector-python)
        * [Node.js](https://github.com/nham-quoc-huan/axvn-connector-node)
        * [Ruby](https://github.com/nham-quoc-huan/axvn-connector-ruby)
        * [DotNET C#](https://github.com/nham-quoc-huan/axvn-connector-dotnet)
        * [Java](https://github.com/nham-quoc-huan/axvn-connector-java)
        * [Rust](https://github.com/nham-quoc-huan/axvn-spot-connector-rust)
        * [PHP](https://github.com/nham-quoc-huan/axvn-connector-php)
        * [Go](https://github.com/nham-quoc-huan/axvn-connector-go)
        * [TypeScript](https://github.com/nham-quoc-huan/axvn-connector-typescript)
* FIX Connector - Provides access to the exchange using the FIX protocol.
    * [Python](https://github.com/nham-quoc-huan/axvn-fix-connector-python)
* [Swagger](https://github.com/nham-quoc-huan/axvn-api-swagger)
    * A YAML file with RESTful API endpoint definitions based on the OpenAPI specification, along with an interactive Swagger UI page.
* [Spot Testnet](https://testnet.axvn.vn/)
    * Users can use the Spot Testnet to experience SPOT trading.
    * Currently only testable via API; there is no UI.
    * Only endpoints starting with `/api/*` are supported; `/sapi/*` endpoints are not supported.

### Contact Us

* [Axvn API Chinese Telegram Group](https://t.me/axvn_api_chinese) or [Axvn API English Telegram Group](https://t.me/axvn_api_english)
    * Suitable for inquiries about API or WebSocket performance issues.
    * API questions not covered in the documentation can also be asked here.
* [Axvn Developer Forum](https://dev.axvn.vn/)
    * You can ask questions about API or WebSocket code.
* [Axvn Customer Support](https://www.axvn.vn/zh-CN/support-center)
    * For inquiries about accounts, funds, 2FA, etc.
