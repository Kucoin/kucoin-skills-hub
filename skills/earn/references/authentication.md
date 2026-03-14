# KuCoin Authentication Reference

All authenticated endpoints require the following headers to be included with each request.

## Required Headers

| Header | Description |
|--------|-------------|
| `KC-API-KEY` | Your API key |
| `KC-API-SIGN` | Base64-encoded HMAC-SHA256 signature |
| `KC-API-TIMESTAMP` | Request timestamp in milliseconds since Unix epoch |
| `KC-API-PASSPHRASE` | HMAC-SHA256 encrypted passphrase, Base64-encoded |
| `KC-API-KEY-VERSION` | `3` |
| `Content-Type` | `application/json` |

## Signature Generation

### Step 1 – Build the Prehash String

```
prehash = timestamp + method + requestEndpoint + body
```

- **timestamp**: Milliseconds since Unix epoch (same value as `KC-API-TIMESTAMP`).
- **method**: HTTP method in uppercase (`GET`, `POST`, `DELETE`).
- **requestEndpoint**: The request path including query string, e.g., `/api/v1/earn/saving/products?currency=USDT`.
- **body**: The JSON request body string. For requests without a body (`GET`, `DELETE` without body), use an empty string `""`.

### Step 2 – HMAC-SHA256 Sign

Sign the prehash string with your **Secret Key** using HMAC-SHA256, then Base64-encode the result.

```
signature = Base64(HMAC-SHA256(secretKey, prehash))
```

### Step 3 – Encrypt the Passphrase

Encrypt your API passphrase with your **Secret Key** using HMAC-SHA256, then Base64-encode.

```
encryptedPassphrase = Base64(HMAC-SHA256(secretKey, passphrase))
```

## Examples

### curl

```bash
#!/bin/bash

API_KEY="your-api-key"
API_SECRET="your-api-secret"
API_PASSPHRASE="your-api-passphrase"
BASE_URL="https://api.kucoin.com"

TIMESTAMP=$(date +%s000)
METHOD="GET"
ENDPOINT="/api/v1/earn/saving/products?currency=USDT"
BODY=""

# Build prehash string
PREHASH="${TIMESTAMP}${METHOD}${ENDPOINT}${BODY}"

# Generate signature
SIGNATURE=$(echo -n "${PREHASH}" | openssl dgst -sha256 -hmac "${API_SECRET}" -binary | base64)

# Encrypt passphrase
ENCRYPTED_PASSPHRASE=$(echo -n "${API_PASSPHRASE}" | openssl dgst -sha256 -hmac "${API_SECRET}" -binary | base64)

curl -X "${METHOD}" "${BASE_URL}${ENDPOINT}" \
  -H "KC-API-KEY: ${API_KEY}" \
  -H "KC-API-SIGN: ${SIGNATURE}" \
  -H "KC-API-TIMESTAMP: ${TIMESTAMP}" \
  -H "KC-API-PASSPHRASE: ${ENCRYPTED_PASSPHRASE}" \
  -H "KC-API-KEY-VERSION: 3" \
  -H "Content-Type: application/json"
```

### Python

```python
import hashlib
import hmac
import base64
import time
import requests

api_key = "your-api-key"
api_secret = "your-api-secret"
api_passphrase = "your-api-passphrase"
base_url = "https://api.kucoin.com"

timestamp = str(int(time.time() * 1000))
method = "GET"
endpoint = "/api/v1/earn/saving/products?currency=USDT"
body = ""

# Build prehash string
prehash = timestamp + method + endpoint + body

# Generate signature
signature = base64.b64encode(
    hmac.new(
        api_secret.encode("utf-8"),
        prehash.encode("utf-8"),
        hashlib.sha256
    ).digest()
).decode("utf-8")

# Encrypt passphrase
encrypted_passphrase = base64.b64encode(
    hmac.new(
        api_secret.encode("utf-8"),
        api_passphrase.encode("utf-8"),
        hashlib.sha256
    ).digest()
).decode("utf-8")

headers = {
    "KC-API-KEY": api_key,
    "KC-API-SIGN": signature,
    "KC-API-TIMESTAMP": timestamp,
    "KC-API-PASSPHRASE": encrypted_passphrase,
    "KC-API-KEY-VERSION": "3",
    "Content-Type": "application/json",
}

response = requests.get(base_url + endpoint, headers=headers)
print(response.json())
```
