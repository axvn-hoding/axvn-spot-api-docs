# API Key Types

Axvn API requires an API Key to access authenticated endpoints for trading, account history, etc.

We support multiple types of API keys:

- Ed25519 (recommended)
- HMAC
- RSA

This document provides an overview of the supported API Keys.

**We recommend using Ed25519 API keys**, as they offer the best performance and security among all supported API key types.

Please read the [REST API](../rest-api_CN.md#request-security) or [WebSocket API](../web-socket-api_CN.md#request-security) documentation to learn how to use different API Key types.

### Ed25519

Ed25519 keys use asymmetric encryption.
You only share your public key with Axvn and use your private key locally to sign API requests.
The Axvn API uses the public key to verify your request signatures.

Ed25519 Keys provide security equivalent to 3072-bit RSA keys, but with smaller keys, smaller signatures, and faster signature computation.

**We recommend using Ed25519 API keys**

Ed25519 key example:

```
-----BEGIN PUBLIC KEY-----
MCowBQYDK2VwAyEAgmDRTtj2FA+wzJUIlAL9ly1eovjLBu7uXUFR+jFULmg=
-----END PUBLIC KEY-----
```

Ed25519 signature example:

```
E7luAubOlcRxL10iQszvNCff+xJjwJrfajEHj1hOncmsgaSB4NE+A/BbQhCWwit/usNJ32/LeTwDYPoA7Qz4BA==
```

### HMAC

HMAC keys use symmetric encryption.
Axvn generates and shares a secret key with you, which you can use to sign API requests.
The Axvn API uses the same shared secret key to verify your request signatures.

HMAC signatures are quick to compute and compact.<br>
However, since the shared secret key must be shared between multiple parties, this is less secure than the asymmetric encryption used by Ed25519 or RSA keys.

**HMAC keys are not recommended.** We recommend switching to and using asymmetric API Keys such as Ed25519 or RSA.

HMAC key example:

```
Fhs4lGae2qAi6VNjbJjebUAwXrIChb7mlf372UOICMwdKaNdNBGKtfdeUff2TTTT
```

HMAC signature example:

```
7f3fc79c57d7a70d2b644ad4589672f4a5d55a62af2a336a0af7d4896f8d48b8
```

### RSA

RSA keys use asymmetric encryption. <br>
You only share your public key with Axvn and use your private key locally to sign API requests.
The Axvn API uses the public key to verify your request signatures.

We support 2048 and 4096-bit RSA keys.

Although RSA keys are more secure than HMAC keys, RSA signatures are much larger than HMAC and Ed25519, which reduces performance.

RSA (2048 bits) example:

```
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyfKiFXpcOhF5rX1XxePN
akwN7Etwtn3v05cZNY+ftDHbVZHs/kY6Ruj5lhxVFAq5dv7Ba9/4jPijXuMuIc6Y
8nUlqtrrxC8DEOAczw9SKATDYZN9nbLfYlbBFfHzRQUXdAtYCPI6XtxmJBS7aOBb
4nZe1SVm+bhLrp0YQnx2P0s+37qkGeVn09m6w9MnWxjgCkkYFPWQkXIu5qOnwx6p
NfqDmFD7d7dUc/6PZQ1bKFALu/UETsobmBk82ShbrBhlc0JXuhf9qBR7QASjHjFQ
2N+VF2PfH8dm5prZIpz/MFKPkBW4Yuss0OXiD+jQt1J2JUKspLqsIqoXjHQQGjL7
3wIDAQAB
-----END PUBLIC KEY-----
```

RSA (2048 bits) signature example::

```
wS6q6h77AvH1TqwInoTDdWIIubRCiUP4RLG++GI24twL3BMtX0EEV+YT1eH8Hb8bLe0Rb9OhOHbt1CC3aurzoCTgZvhNek47mg+Bpu8fwQ7eRkXEiWBx5C8BNN73JwnnkZw4UzYvqiwAs162jToV8AL0eN043KJ3MEKCy3C6nyeYOFSg+1Cp637KtAZk3z7aHknSu7/PXSPuwMIpBgFctf8YKGZFAVRbgwlcgUDhXyaGts6OFePGy0jkZKJHawb/w5hoatatsfVmVC4hZ8fsfystQ9k5DNjTm7ROApWaXy9BsfAYcj13O424mqlpkKG4EGnIjOIWB/pRDDQEm2O/xg==
```
