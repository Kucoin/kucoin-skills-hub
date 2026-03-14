# KuCoin API Authentication

KuCoin uses HMAC-SHA256 signing for authenticated API requests.

## Required Headers

| Header | Description |
|--------|-------------|
| `KC-API-KEY` | Your API Key |
| `KC-API-SIGN` | Base64-encoded HMAC-SHA256 signature |
| `KC-API-TIMESTAMP` | Request timestamp in milliseconds (UTC) |
| `KC-API-PASSPHRASE` | HMAC-SHA256 encrypted passphrase, Base64-encoded |
| `KC-API-KEY-VERSION` | `3` |

## Signing Process

### Step 1: Build the Prehash String

Concatenate the following values into a single string:

```
prehash = timestamp + method + endpoint + body
```

- **timestamp**: Current time in milliseconds (e.g., `1672531200000`)
- **method**: HTTP method in uppercase (e.g., `GET`, `POST`, `DELETE`)
- **endpoint**: Request path including query string (e.g., `/api/v3/margin/accounts?quoteCurrency=USDT`)
- **body**: JSON request body as a string (empty string `""` for GET requests)

### Step 2: Generate the Signature

Sign the prehash string using HMAC-SHA256 with your **Secret Key**, then Base64-encode the result:

```
signature = Base64(HMAC-SHA256(secretKey, prehash))
```

### Step 3: Encrypt the Passphrase

Encrypt the passphrase using HMAC-SHA256 with your **Secret Key**, then Base64-encode:

```
encryptedPassphrase = Base64(HMAC-SHA256(secretKey, passphrase))
```

### Step 4: Send the Request

Include all five headers in the HTTP request.

## Examples

### curl Example

```bash
#!/bin/bash

API_KEY="your-api-key"
API_SECRET="your-api-secret"
API_PASSPHRASE="your-api-passphrase"

# Timestamp in milliseconds
TIMESTAMP=$(python3 -c "import time; print(int(time.time() * 1000))")

# Request details
METHOD="GET"
ENDPOINT="/api/v3/margin/accounts"
BODY=""

# Build prehash string
PREHASH="${TIMESTAMP}${METHOD}${ENDPOINT}${BODY}"

# Generate signature (Base64 HMAC-SHA256)
SIGNATURE=$(echo -n "${PREHASH}" | openssl dgst -sha256 -hmac "${API_SECRET}" -binary | base64)

# Encrypt passphrase (Base64 HMAC-SHA256)
ENCRYPTED_PASSPHRASE=$(echo -n "${API_PASSPHRASE}" | openssl dgst -sha256 -hmac "${API_SECRET}" -binary | base64)

# Send request
curl -s -X "${METHOD}" \
  "https://api.kucoin.com${ENDPOINT}" \
  -H "KC-API-KEY: ${API_KEY}" \
  -H "KC-API-SIGN: ${SIGNATURE}" \
  -H "KC-API-TIMESTAMP: ${TIMESTAMP}" \
  -H "KC-API-PASSPHRASE: ${ENCRYPTED_PASSPHRASE}" \
  -H "KC-API-KEY-VERSION: 3" \
  -H "Content-Type: application/json" \
  -H "User-Agent: kucoin-margin-trading/1.0.0 (Skill)"
```

### Python Example

```python
import hashlib
import hmac
import base64
import time
import requests

API_KEY = "your-api-key"
API_SECRET = "your-api-secret"
API_PASSPHRASE = "your-api-passphrase"
BASE_URL = "https://api.kucoin.com"


def sign_request(method: str, endpoint: str, body: str = "") -> dict:
    """Generate authentication headers for a KuCoin API request."""
    timestamp = str(int(time.time() * 1000))

    # Step 1: Build the prehash string
    prehash = timestamp + method.upper() + endpoint + body

    # Step 2: Generate the signature
    signature = base64.b64encode(
        hmac.new(
            API_SECRET.encode("utf-8"),
            prehash.encode("utf-8"),
            hashlib.sha256,
        ).digest()
    ).decode("utf-8")

    # Step 3: Encrypt the passphrase
    encrypted_passphrase = base64.b64encode(
        hmac.new(
            API_SECRET.encode("utf-8"),
            API_PASSPHRASE.encode("utf-8"),
            hashlib.sha256,
        ).digest()
    ).decode("utf-8")

    # Step 4: Return headers
    return {
        "KC-API-KEY": API_KEY,
        "KC-API-SIGN": signature,
        "KC-API-TIMESTAMP": timestamp,
        "KC-API-PASSPHRASE": encrypted_passphrase,
        "KC-API-KEY-VERSION": "3",
        "Content-Type": "application/json",
        "User-Agent": "kucoin-margin-trading/1.0.0 (Skill)",
    }


# Example: Get cross margin account
method = "GET"
endpoint = "/api/v3/margin/accounts"
headers = sign_request(method, endpoint)
response = requests.get(BASE_URL + endpoint, headers=headers)
print(response.json())


# Example: Borrow USDT on cross margin
import json

method = "POST"
endpoint = "/api/v3/margin/borrow"
body_dict = {
    "currency": "USDT",
    "size": "100",
    "timeInForce": "IOC",
}
body = json.dumps(body_dict)
headers = sign_request(method, endpoint, body)
response = requests.post(BASE_URL + endpoint, headers=headers, data=body)
print(response.json())
```

## Important Notes

1. **Timestamp tolerance**: The server accepts timestamps within a 5-second window. Ensure your system clock is synchronized.
2. **Key Version**: Always set `KC-API-KEY-VERSION` to `3` for the latest authentication protocol.
3. **GET requests**: Use an empty string (`""`) as the body when computing the signature for GET requests.
4. **Query parameters**: Include the full query string in the endpoint when computing the signature (e.g., `/api/v3/margin/accounts?quoteCurrency=USDT`).
5. **Body format**: For POST requests, the body must be a valid JSON string matching exactly what is sent in the request.
