# KuCoin API Authentication

KuCoin uses HMAC-SHA256 to authenticate private API requests. Every authenticated request must include the following HTTP headers.

## Required Headers

| Header | Description |
|--------|-------------|
| `KC-API-KEY` | Your API key obtained from the KuCoin console. |
| `KC-API-SIGN` | Base64-encoded HMAC-SHA256 signature of the prehash string, using your API secret as the key. |
| `KC-API-TIMESTAMP` | Request timestamp in **milliseconds** since Unix epoch. Must be within 5 seconds of server time. |
| `KC-API-PASSPHRASE` | The passphrase you set when creating the API key. For API key version 2, this must be Base64-encoded HMAC-SHA256 of the raw passphrase using the API secret. |
| `KC-API-KEY-VERSION` | API key version. Use `"2"` for the latest version (recommended). Version 2 requires the passphrase to be signed. |

## Signature Construction

### Step 1: Build the Prehash String

The prehash string is constructed by concatenating the following components **without any separator**:

```
prehash = timestamp + method + endpoint + body
```

| Component | Description | Example |
|-----------|-------------|---------|
| `timestamp` | Same value as `KC-API-TIMESTAMP` (milliseconds) | `1721969436000` |
| `method` | HTTP method in **UPPERCASE** | `GET`, `POST`, `DELETE` |
| `endpoint` | Request path including query string (no host) | `/api/v1/orders?symbol=XBTUSDTM` |
| `body` | JSON request body as a string. Empty string `""` for GET/DELETE requests with no body. | `{"symbol":"XBTUSDTM","side":"buy","type":"market","size":1,"leverage":"10"}` |

**Examples of prehash strings:**

GET request:
```
1721969436000GET/api/v1/orders?symbol=XBTUSDTM
```

POST request:
```
1721969436000POST/api/v1/orders{"clientOid":"agent-abc123","symbol":"XBTUSDTM","side":"buy","type":"market","size":1,"leverage":"10"}
```

DELETE request:
```
1721969436000DELETE/api/v1/orders/5c35c02703aa673ceec2a168
```

### Step 2: Compute the HMAC-SHA256 Signature

Sign the prehash string using your **API Secret** as the HMAC key, then Base64-encode the result:

```
signature = Base64( HMAC-SHA256( apiSecret, prehash ) )
```

### Step 3: Sign the Passphrase (API Key Version 2)

For API key version `"2"`, the passphrase must also be signed:

```
signedPassphrase = Base64( HMAC-SHA256( apiSecret, rawPassphrase ) )
```

Set this as the value of the `KC-API-PASSPHRASE` header.

### Step 4: Set Headers

Include all five headers in the request:

```
KC-API-KEY: <your-api-key>
KC-API-SIGN: <signature>
KC-API-TIMESTAMP: <timestamp>
KC-API-PASSPHRASE: <signedPassphrase>
KC-API-KEY-VERSION: 2
```

## Base URLs

| API | Base URL |
|-----|----------|
| Spot / Pro API | `https://api.kucoin.com` |
| Classic Futures API | `https://api-futures.kucoin.com` |

## Code Examples

### curl

```bash
#!/bin/bash

# Configuration
API_KEY="your-api-key"
API_SECRET="your-api-secret"
API_PASSPHRASE="your-api-passphrase"
BASE_URL="https://api-futures.kucoin.com"

# Request details
METHOD="GET"
ENDPOINT="/api/v1/orders?symbol=XBTUSDTM"
BODY=""

# Generate timestamp (milliseconds)
TIMESTAMP=$(date +%s000)

# Build prehash string
PREHASH="${TIMESTAMP}${METHOD}${ENDPOINT}${BODY}"

# Generate signature (Base64-encoded HMAC-SHA256)
SIGNATURE=$(echo -n "${PREHASH}" | openssl dgst -sha256 -hmac "${API_SECRET}" -binary | base64)

# Sign passphrase for API key version 2
SIGNED_PASSPHRASE=$(echo -n "${API_PASSPHRASE}" | openssl dgst -sha256 -hmac "${API_SECRET}" -binary | base64)

# Make the request
curl -s -X "${METHOD}" "${BASE_URL}${ENDPOINT}" \
  -H "Content-Type: application/json" \
  -H "KC-API-KEY: ${API_KEY}" \
  -H "KC-API-SIGN: ${SIGNATURE}" \
  -H "KC-API-TIMESTAMP: ${TIMESTAMP}" \
  -H "KC-API-PASSPHRASE: ${SIGNED_PASSPHRASE}" \
  -H "KC-API-KEY-VERSION: 2" \
  -H "User-Agent: kucoin-futures-trading/1.0.0 (Skill)"
```

#### curl POST Example

```bash
#!/bin/bash

# Configuration
API_KEY="your-api-key"
API_SECRET="your-api-secret"
API_PASSPHRASE="your-api-passphrase"
BASE_URL="https://api-futures.kucoin.com"

# Request details
METHOD="POST"
ENDPOINT="/api/v1/orders"
BODY='{"clientOid":"agent-abc123","symbol":"XBTUSDTM","side":"buy","type":"market","size":1,"leverage":"10"}'

# Generate timestamp (milliseconds)
TIMESTAMP=$(date +%s000)

# Build prehash string
PREHASH="${TIMESTAMP}${METHOD}${ENDPOINT}${BODY}"

# Generate signature (Base64-encoded HMAC-SHA256)
SIGNATURE=$(echo -n "${PREHASH}" | openssl dgst -sha256 -hmac "${API_SECRET}" -binary | base64)

# Sign passphrase for API key version 2
SIGNED_PASSPHRASE=$(echo -n "${API_PASSPHRASE}" | openssl dgst -sha256 -hmac "${API_SECRET}" -binary | base64)

# Make the request
curl -s -X "${METHOD}" "${BASE_URL}${ENDPOINT}" \
  -H "Content-Type: application/json" \
  -H "KC-API-KEY: ${API_KEY}" \
  -H "KC-API-SIGN: ${SIGNATURE}" \
  -H "KC-API-TIMESTAMP: ${TIMESTAMP}" \
  -H "KC-API-PASSPHRASE: ${SIGNED_PASSPHRASE}" \
  -H "KC-API-KEY-VERSION: 2" \
  -H "User-Agent: kucoin-futures-trading/1.0.0 (Skill)" \
  -d "${BODY}"
```

### Python

```python
import hashlib
import hmac
import base64
import time
import json
import requests


def create_signature(api_secret: str, timestamp: str, method: str, endpoint: str, body: str = "") -> str:
    """Create HMAC-SHA256 signature for KuCoin API authentication."""
    prehash = timestamp + method + endpoint + body
    signature = hmac.new(
        api_secret.encode("utf-8"),
        prehash.encode("utf-8"),
        hashlib.sha256,
    ).digest()
    return base64.b64encode(signature).decode("utf-8")


def sign_passphrase(api_secret: str, passphrase: str) -> str:
    """Sign the passphrase for API key version 2."""
    signed = hmac.new(
        api_secret.encode("utf-8"),
        passphrase.encode("utf-8"),
        hashlib.sha256,
    ).digest()
    return base64.b64encode(signed).decode("utf-8")


def build_headers(api_key: str, api_secret: str, passphrase: str, method: str, endpoint: str, body: str = "") -> dict:
    """Build authenticated headers for a KuCoin API request."""
    timestamp = str(int(time.time() * 1000))
    signature = create_signature(api_secret, timestamp, method, endpoint, body)
    signed_passphrase = sign_passphrase(api_secret, passphrase)

    return {
        "KC-API-KEY": api_key,
        "KC-API-SIGN": signature,
        "KC-API-TIMESTAMP": timestamp,
        "KC-API-PASSPHRASE": signed_passphrase,
        "KC-API-KEY-VERSION": "2",
        "Content-Type": "application/json",
        "User-Agent": "kucoin-futures-trading/1.0.0 (Skill)",
    }


# --- Example: GET request (list orders) ---

API_KEY = "your-api-key"
API_SECRET = "your-api-secret"
API_PASSPHRASE = "your-api-passphrase"
BASE_URL = "https://api-futures.kucoin.com"

method = "GET"
endpoint = "/api/v1/orders?symbol=XBTUSDTM"

headers = build_headers(API_KEY, API_SECRET, API_PASSPHRASE, method, endpoint)
response = requests.get(BASE_URL + endpoint, headers=headers)
print(response.json())


# --- Example: POST request (place market order) ---

method = "POST"
endpoint = "/api/v1/orders"
body_dict = {
    "clientOid": "agent-abc123",
    "symbol": "XBTUSDTM",
    "side": "buy",
    "type": "market",
    "size": 1,
    "leverage": "10",
}
body = json.dumps(body_dict, separators=(",", ":"))

headers = build_headers(API_KEY, API_SECRET, API_PASSPHRASE, method, endpoint, body)
response = requests.post(BASE_URL + endpoint, headers=headers, data=body)
print(response.json())
```

## Important Notes

1. **Timestamp tolerance**: The server rejects requests whose timestamp differs by more than 5 seconds from the server time. Use `GET /api/v1/timestamp` to synchronize if needed.
2. **Body serialization**: When computing the signature for POST requests, the JSON body string used in the prehash must be **identical** to the body sent in the HTTP request. Use compact serialization (no extra whitespace).
3. **Query string**: For GET requests with query parameters, the full path including the query string (e.g., `/api/v1/orders?symbol=XBTUSDTM`) is included in the prehash.
4. **Empty body**: For GET and DELETE requests without a body, use an empty string `""` as the body component in the prehash.
5. **API key version**: Always use version `"2"` for new integrations. Version 1 sends the passphrase in plain text, which is less secure.
