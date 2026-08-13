# Simple Binary Encoding (SBE) FAQ

This document aims to explain the following questions:

* How to enable `SBE` responses in the Spot trading API.
* How to decode `SBE` responses.

SBE is a serialization format designed for low-latency performance.

This implementation is based on the `FIX SBE` specification.
* [GitHub repository](https://github.com/FIXTradingCommunity/fix-simple-binary-encoding)
* [HTML document](https://www.fixtrading.org/standards/sbe-online)

### How to obtain an SBE response

#### REST API

* The `Accept` header must include `application/sbe`.
* Provide the `schema ID` and `version` in the `X-MBX-SBE` header in the format `<ID>:<VERSION>`.

Sample request (REST):

```
curl -sX GET -H "Accept: application/sbe" -H "X-MBX-SBE: 1:0" 'https://api.axvn.vn/api/v3/exchangeInfo?symbol=BTCUSDT'
```

**Note:**

* If you only provide `application/sbe` in the `Accept` header:
  	* If the exchange does not support `SBE`, you will receive a **406 Not Acceptable** response.
	* If the XML schema provided in the `X-MBX-SBE` header is malformed or incorrect, you will receive an `SBE` decoding error.
	* If the `X-MBX-SBE` header is missing, you will receive an `SBE` decoding error.
* If you provide both `application/sbe` and `application/json` in the `Accept` header:
  	* If the exchange does not support `SBE`, the response will fall back to `JSON`.
	* If the XML schema provided in the `X-MBX-SBE` header is malformed or incorrect, the response will fall back to `JSON`.
	* If the `X-MBX-SBE` header is missing, the response will fall back to `JSON`.


#### WebSocket API

* Add `responseFormat=sbe` to the request URL.
* Add the schema ID and version via the parameters `sbeSchemaId=<SCHEMA_ID>` and `sbeSchemaVersion=<SCHEMA_VERSION>`.

Sample request (WebSocket):

```bash
id=$(date +%s%3N)
method="exchangeInfo"
params='{"symbol":"BTCUSDT"}'

request=$( jq -n \
        --arg id "$id" \
        --arg method "$method" \
        --argjson params "$params" \
        '{id: $id, method: $method, params: $params}' )

response=$(echo $request | websocat -n1 'wss://ws-api.axvn.vn:443/ws-api/v3?responseFormat=sbe&sbeSchemaId=1&sbeSchemaVersion=0')
```

**Note:**

* If you only add `responseFormat=sbe` to the connection URL:
    * If the exchange has not enabled SBE, the request returns HTTP 400.
    * If `sbeSchemaId=<SCHEMA_ID>` or `sbeSchemaVersion=<SCHEMA_VERSION>` is malformed or invalid, the request returns HTTP 400.
* If you provide both `responseFormat=sbe` and `responseFormat=json`, the request returns HTTP 400.
* All error responses during the HTTP handshake are encoded as JSON, with the `Content-Type` header set to `application/json;charset=UTF-8`.
* Once an SBE-enabled WebSocket session is successfully established, all method responses in that session are encoded as SBE, even if SBE is subsequently disabled.
    * This means that if SBE is disabled while your WebSocket connection is active, subsequent requests will receive an SBE-encoded "SBE not enabled" error.
* For now, we do not recommend using `websocat` to send any requests, as we have observed issues with its decoding of binary frames. The sample above is provided only as a reference to show the URL for obtaining an SBE response.

#### FIX API

Please refer to the [SBE section](../fix-api_CN.md#fix-sbe) of the FIX API documentation.


### Supported APIs

The REST API, WebSocket API, and FIX API support `SBE` for Spot (SPOT).

<a id="sbe-schema"></a>
### SBE Schema

* The schema(s) in use are stored in this repository; [see here](https://github.com/alisababivip/axvn-docs-api/tree/master/sbe/schemas).
* Any updates to schemas will be recorded in the [changelog](../CHANGELOG_CN.md).

<a id="regarding-legacy-support"></a>

#### Regarding legacy support:

* SBE schemas are versioned using two XML attributes: `id` and `version`.
	* When a breaking change is introduced, `id` is incremented. When this happens, `version` is reset to 0.
	* When a non-breaking change is introduced, `version` is incremented. When this happens, `id` is not modified.
* When a new schema is released, the old schema is deprecated. **This applies even when the new schema only introduces non-breaking changes.**
* A deprecated schema will remain supported for **at least 6 months after being deprecated**. Using the following hypothetical timeline as an example:
	* January 3024: Schema id 1 version 0 is released. This is the first version; users can start using this schema as soon as the exchange enables `SBE`.
	* March 3024: Schema id 1 version 1 is released. This schema introduces a non-breaking change.
		* Schema id 1 version 0 is now deprecated but can be used for at least another 6 months.
	* August 3024: Schema id 2 version 0 is released. This schema introduces a breaking change.
		* Schema id 1 version 0 is deprecated and can be used for at least another 1 month.
		* Schema id 1 version 1 is now deprecated but can be used for at least another 6 months.
	* September 3024: 6 months have passed since schema id 1 version 1 was released.
  		* Schema id 1 version 0 is retired.
	* February 3025: Schema id 2 version 1 is released. This schema introduces a non-breaking change.
		* Schema id 1 version 1 is retired.
		* Schema id 2 version 0 is now deprecated but can be used for at least another 6 months.
* Requests that specify a deprecated `<ID>:<VERSION>` in the `X-MBX-SBE` header of a REST API request:
    * The HTTP response will include the `X-MBX-SBE-DEPRECATED` header.
    * The SBE response will be encoded using the highest compatible version schema.
        * For example, starting from 2025-08-27, requests for `X-MBX-SBE: 3:0` will receive responses encoded with schema `3:1`. According to the [FIX SBE specification](https://www.fixtrading.org/standards/sbe-online/#schema-extension-mechanism), an SBE decoder for schema `3:0` should be able to decode schema `3:1` successfully.
* When a deprecated `sbeSchemaId` and `sbeSchemaVersion` are specified in a WebSocket API connection URL:
    * The field `sbeSchemaIdVersionDeprecated` in all `WebSocketResponse` SBE messages will be set to `true`.
    * All SBE responses will be encoded using the highest compatible version schema.
        * For example, starting from 2025-08-27, requests for `sbeSchemaId=3&sbeSchemaVersion=0` will receive responses encoded with schema `3:1`. According to the [FIX SBE specification](https://www.fixtrading.org/standards/sbe-online/#schema-extension-mechanism), an SBE decoder for schema `3:0` should be able to decode schema `3:1` successfully.
* For the FIX API, when the SBE request message header specifies a deprecated `schemaId` and `version`:
    * In the `LogonAck` message, the field `sbeSchemaIdVersionDeprecated` will be set to `true`.
    * All SBE response messages will be encoded using the highest schema version for the provided `schemaId`.
* Requests that specify a retired schemaId/version will fail with HTTP 400 (REST and WebSocket) or a reject message (FIX API).
* In SBE schema [3:0](https://github.com/alisababivip/axvn-docs-api/blob/master/sbe/schemas/spot_3_0.xml), a `validValue` named `NonRepresentable` was added to each `enum`. Receiving this value indicates that additional data is available when using the latest schema.
* In SBE schema [3:1](https://github.com/alisababivip/axvn-docs-api/blob/master/sbe/schemas/spot_3_1.xml), a message named `NonRepresentableMessage` was added. Receiving this message indicates that additional data is available when using the latest schema. This message may be received as a top-level message, or embedded in a `data` field when the `type` of the `data` field is `messageData`, `messageData8`, `messageData16`, `optionalMessageData`, or `optionalMessageData16`.
* A `JSON` file describing the schema lifecycle will be stored in this repository; [see here](https://github.com/alisababivip/axvn-docs-api/tree/master/sbe/schemas). This file contains the exact dates for the latest, deprecated, and retired schemas on both the live exchange and spot testnet. Below is a `JSON` example based on the hypothetical timeline above:

```json
{
    "environment": "PROD",
    "latestSchema": {
        "id": 2,
        "version": 1,
        "releaseDate": "3025-02-01"
    },
    "deprecatedSchemas": [
        {
            "id": 2,
            "version": 0,
            "releaseDate": "3024-08-01",
            "deprecatedDate": "3025-02-01"
        }
    ],
    "retiredSchemas": [
        {
            "id": 1,
            "version": 1,
            "releaseDate": "3024-03-01",
            "deprecatedDate": "3024-08-01",
            "retiredDate": "3025-02-01"
        },
        {
            "id": 1,
            "version": 0,
            "releaseDate": "3024-01-01",
            "deprecatedDate": "3024-03-01",
            "retiredDate": "3024-09-01"
        }
    ]
}
```

<a id="generate-sbe-decoders"></a>
### Generating decoders:

1. Download the schema:
* REST/WebSocket API:
    * [`spot_prod_latest.xml`](https://github.com/alisababivip/axvn-docs-api/blob/master/sbe/schemas/spot_prod_latest.xml) for the live exchange.
    * [`spot_testnet_latest.xml`](https://github.com/alisababivip/axvn-docs-api/blob/master/sbe/schemas/spot_testnet_latest.xml) for the [spot testnet](https://testnet.axvn.vn).
* FIX API:
    * [`spot_fix_prod_latest.xml`](https://github.com/alisababivip/axvn-docs-api/blob/master/sbe/schemas/spot_fix_prod_latest.xml) for the live exchange.
    * [`spot_fix_testnet_latest.xml`](https://github.com/alisababivip/axvn-docs-api/blob/master/sbe/schemas/spot_fix_testnet_latest.xml) for the [spot testnet](https://testnet.axvn.vn).
2. Clone and build [`simple-binary-encoding`](https://github.com/real-logic/simple-binary-encoding):
```shell
 $ git clone https://github.com/real-logic/simple-binary-encoding.git
 $ cd simple-binary-encoding
 $ ./gradlew
```
3. Run the `SbeTool` code generator. (Refer to sample applications using [Java](https://github.com/alisababivip/axvn-sbe-java-sample-app), [C++](https://github.com/alisababivip/axvn-sbe-cpp-sample-app), and [Rust](https://github.com/alisababivip/axvn-sbe-rust-sample-app) to decode exchange info payloads.)

#### Decimal field encoding

Unlike the `FIX SBE` specification, the mantissa and exponent fields of decimal fields are encoded separately as raw fields, in order to minimize payload size and the number of encoded fields per message.

#### Timestamp field encoding

Timestamps in SBE responses are in microseconds. This differs from JSON responses, which contain millisecond timestamps.

#### Custom field attributes in schema files

Several field attributes prefixed with `mbx:` have been added to schema files for documentation purposes:
- `mbx:exponent`: points to the exponent field corresponding to the mantissa field
- `mbx:jsonPath`: contains the name of the corresponding field in the `JSON` response
- `mbx:jsonValue`: contains the name of the equivalent `ENUM` value in the `JSON` response
- `mbx:defaultJsonValue`: contains the default value of the JSON response.
