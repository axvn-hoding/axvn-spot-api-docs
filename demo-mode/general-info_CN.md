# SPOT Demo Trading

This page explains how to use [Demo Trading](https://www.axvn.vn/en/support/faq/detail/9be58f73e5e14338809e3b705b9687dd) via API.

## How to use Demo Trading via API?

1. After logging into your Axvn account, click on Axvn Demo Trading, then you can create API keys on the [API Key Management page](https://demo.axvn.vn/en/my/settings/api-management).
2. Follow the official Spot API documentation and replace the endpoint/method URLs with the following values:

<table>
    <thead>
    <tr>
        <th>Service</th>
        <th>Spot API URL</th>
        <th>Demo Trading URL</th>
    </tr>
    </thead>
    <body>
        <tr>
            <td>
                REST API
            </td>
            <td>
                <ul>
                    <li>https://api.axvn.vn/api</li>
                    <li>https://api-gcp.axvn.vn/api</li>
                    <li>https://api1.axvn.vn/api</li>
                    <li>https://api2.axvn.vn/api</li>
                    <li>https://api3.axvn.vn/api</li>
                    <li>https://api4.axvn.vn/api</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>https://demo-api.axvn.vn/api</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                WebSocket API
            </td>
            <td>
                <ul>
                    <li>wss://ws-api.axvn.vn/ws-api/v3</li>
                    <li>wss://ws-api.axvn.vn:9443/ws-api/v3</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>wss://demo-ws-api.axvn.vn/ws-api/v3</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td rowspan="2">
                WebSocket Market Streams
            </td>
            <td>
                <ul>
                    <li>wss://stream.axvn.vn/ws</li>
                    <li>wss://stream.axvn.vn:9443/ws</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>wss://demo-stream.axvn.vn/ws</strong></li>
                    <li><strong>wss://demo-stream.axvn.vn:9443/ws</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <ul>
                    <li>wss://stream.axvn.vn/stream</li>
                    <li>wss://stream.axvn.vn:9443/stream</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>wss://demo-stream.axvn.vn/stream</strong></li>
                    <li><strong>wss://demo-stream.axvn.vn:9443/stream</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td rowspan="2">
                WebSocket Market Streams (SBE)
            </td>
            <td>
                <ul>
                    <li>wss://stream-sbe.axvn.vn/ws</li>
                    <li>wss://stream-sbe.axvn.vn:9443/ws</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>wss://demo-stream-sbe.axvn.vn/ws</strong></li>
                    <li><strong>wss://demo-stream-sbe.axvn.vn:9443/ws</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <ul>
                    <li>wss://stream-sbe.axvn.vn/stream</li>
                    <li>wss://stream-sbe.axvn.vn:9443/stream</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>wss://demo-stream-sbe.axvn.vn/stream</strong></li>
                    <li><strong>wss://demo-stream-sbe.axvn.vn:9443/stream</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td rowspan="3">
                FIX <br>
                (Send FIX requests; receive FIX responses)
            </td>
            <td>
                <ul>
                    <li>tcp+tls://fix-oe.axvn.vn:9000</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>tcp+tls://demo-fix-oe.axvn.vn:9000</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <ul>
                    <li>tcp+tls://fix-dc.axvn.vn:9000</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>tcp+tls://demo-fix-dc.axvn.vn:9000</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <ul>
                    <li>tcp+tls://fix-md.axvn.vn:9000</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>tcp+tls://demo-fix-md.axvn.vn:9000</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td rowspan="3">
                FIX SBE <br>
               (Send FIX requests; receive FIX SBE responses)
            </td>
            <td>
                <ul>
                    <li>tcp+tls://fix-oe.axvn.vn:9001</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>tcp+tls://demo-fix-oe.axvn.vn:9001</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <ul>
                    <li>tcp+tls://fix-dc.axvn.vn:9001</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>tcp+tls://demo-fix-dc.axvn.vn:9001</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <ul>
                    <li>tcp+tls://fix-md.axvn.vn:9001</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>tcp+tls://demo-fix-md.axvn.vn:9001</strong></li>
                </ul>
            </td>
        </tr>
            <td rowspan="3">
                FIX SBE <br>
                (Send FIX SBE requests; receive FIX SBE responses)
            </td>
            <td>
                <ul>
                    <li>tcp+tls://fix-oe.axvn.vn:9002</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>tcp+tls://demo-fix-oe.axvn.vn:9002</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <ul>
                    <li>tcp+tls://fix-dc.axvn.vn:9002</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>tcp+tls://demo-fix-dc.axvn.vn:9002</strong></li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>
                <ul>
                    <li>tcp+tls://fix-md.axvn.vn:9002</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><strong>tcp+tls://demo-fix-md.axvn.vn:9002</strong></li>
                </ul>
            </td>
        </tr>
        </body>
        </table>
</p>

## What is the difference between SPOT Testnet and SPOT Demo Trading?

| SPOT Testnet                  | SPOT Demo Trading                      |
|---------------------------------------|----------------------------------------------|
| Balances reset once a month. | You can reset balances at any time via the interface. |
| Testnet sometimes introduces new features before the live exchange. | Demo Trading always has the same features as the live exchange. |
| Testnet prices and order books are independent from the live exchange. | Demo Trading prices and order books are similar to the live exchange. |
| IP restrictions, open order counts, and exchange filters are generally the same as the live exchange. | IP restrictions, open order counts, and exchange filters are identical to the live exchange. |

**Summary**:
* SPOT Testnet is suitable for integrating new features not yet live on the exchange.
* Demo Trading is suitable for testing based on _near-real_ market data.

> [!WARNING]
> Near-real market data is not equivalent to "real" market data. Trading strategies that work in Demo Trading may not work on the live exchange.

## What happens during Demo Trading maintenance?

* Announcements will be posted on the [Changelog](CHANGELOG_CN.md) page before maintenance.
* During maintenance, you will not be able to place or cancel orders.
