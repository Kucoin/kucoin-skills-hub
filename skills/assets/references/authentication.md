# KuCoin API Authentication

KuCoin uses HMAC-SHA256 to authenticate API requests. Every authenticated request must include the following HTTP headers.

## Required Headers

| Header | Description |
|--------|-------------|
| `KC-API-KEY` | Your API key, obtained from the KuCoin API management page. |
| `KC-API-SIGN` | Base64-encoded HMAC-SHA256 signature of the prehash string, using your API secret as the key. |
| `KC-API-TIMESTAMP` | Request timestamp in **milliseconds** since Unix epoch (e.g., `1718000000000`). The server rejects requests where the timestamp differs from server time by more than 5 seconds. |
| `KC-API-PASSPHRASE` | The passphrase you specified when creating the API key. For API Key Version 2, this must be encrypted: Base64(HMAC-SHA256(passphrase, secretKey)). For Version 1, send the plain passphrase. |
| `KC-API-KEY-VERSION` | API key version. Use `"2"` for keys created after a certain date (recommended). Use `"1"` for legacy keys. |

## Signature Construction

### Step 1 -- Build the Prehash String

```
prehash = timestamp + method + endpoint + body
```

| Component | Description | Example |
|-----------|-------------|---------|
| `timestamp` | Same value sent in `KC-API-TIMESTAMP` (milliseconds). | `1718000000000` |
| `method` | HTTP method in **uppercase**. | `GET`, `POST`, `DELETE` |
| `endpoint` | Request path including query string (no host). | `/api/v1/accounts?currency=BTC` |
| `body` | JSON request body as a string. Empty string `""` for GET/DELETE requests with no body. | `{"currency":"BTC","amount":"0.1"}` |

**Examples:**

- **GET** request: `1718000000000GET/api/v1/accounts?currency=BTC`
- **POST** request: `1718000000000POST/api/v1/orders{"clientOid":"abc123","side":"buy","symbol":"BTC-USDT","type":"market","size":"0.001"}`
- **DELETE** request: `1718000000000DELETE/api/v1/orders/5c35c02703aa673ceec2a168`

### Step 2 -- Compute HMAC-SHA256

Sign the prehash string with your **API Secret** using HMAC-SHA256, then Base64-encode the result.

```
signature = Base64( HMAC-SHA256( prehash, secretKey ) )
```

### Step 3 -- Encrypt Passphrase (API Key Version 2 Only)

For API Key Version 2, the passphrase header must also be encrypted:

```
encryptedPassphrase = Base64( HMAC-SHA256( passphrase, secretKey ) )
```

For API Key Version 1, send the passphrase as plain text.

## Complete Examples

### curl Example

```bash
#!/bin/bash

# ----- Configuration -----
API_KEY="your-api-key"
API_SECRET="your-api-secret"
API_PASSPHRASE="your-api-passphrase"
API_KEY_VERSION="2"
BASE_URL="https://api.kucoin.com"

# ----- Request parameters -----
METHOD="GET"
ENDPOINT="/api/v1/accounts?currency=BTC"
BODY=""

# ----- Build timestamp (milliseconds) -----
TIMESTAMP=$(python3 -c "import time; print(int(time.time() * 1000))")

# ----- Build prehash string -----
PREHASH="${TIMESTAMP}${METHOD}${ENDPOINT}${BODY}"

# ----- Compute HMAC-SHA256 signature -----
SIGNATURE=$(printf '%s' "${PREHASH}" \
  | openssl dgst -sha256 -hmac "${API_SECRET}" -binary \
  | openssl enc -base64 -A)

# ----- Encrypt passphrase (Version 2) -----
ENCRYPTED_PASSPHRASE=$(printf '%s' "${API_PASSPHRASE}" \
  | openssl dgst -sha256 -hmac "${API_SECRET}" -binary \
  | openssl enc -base64 -A)

# ----- Send request -----
curl -s -X "${METHOD}" "${BASE_URL}${ENDPOINT}" \
  -H "KC-API-KEY: ${API_KEY}" \
  -H "KC-API-SIGN: ${SIGNATURE}" \
  -H "KC-API-TIMESTAMP: ${TIMESTAMP}" \
  -H "KC-API-PASSPHRASE: ${ENCRYPTED_PASSPHRASE}" \
  -H "KC-API-KEY-VERSION: ${API_KEY_VERSION}" \
  -H "Content-Type: application/json" \
  -H "User-Agent: kucoin-assets/1.0.0 (Skill)"
```

#### curl POST Example

```bash
#!/bin/bash

# ----- Configuration -----
API_KEY="your-api-key"
API_SECRET="your-api-secret"
API_PASSPHRASE="your-api-passphrase"
API_KEY_VERSION="2"
BASE_URL="https://api.kucoin.com"

# ----- Request parameters -----
METHOD="POST"
ENDPOINT="/api/v3/withdrawals"
BODY='{"currency":"USDT","address":"0xABC123...","amount":"50","chain":"eth"}'

# ----- Build timestamp (milliseconds) -----
TIMESTAMP=$(python3 -c "import time; print(int(time.time() * 1000))")

# ----- Build prehash string -----
PREHASH="${TIMESTAMP}${METHOD}${ENDPOINT}${BODY}"

# ----- Compute HMAC-SHA256 signature -----
SIGNATURE=$(printf '%s' "${PREHASH}" \
  | openssl dgst -sha256 -hmac "${API_SECRET}" -binary \
  | openssl enc -base64 -A)

# ----- Encrypt passphrase (Version 2) -----
ENCRYPTED_PASSPHRASE=$(printf '%s' "${API_PASSPHRASE}" \
  | openssl dgst -sha256 -hmac "${API_SECRET}" -binary \
  | openssl enc -base64 -A)

# ----- Send request -----
curl -s -X "${METHOD}" "${BASE_URL}${ENDPOINT}" \
  -H "KC-API-KEY: ${API_KEY}" \
  -H "KC-API-SIGN: ${SIGNATURE}" \
  -H "KC-API-TIMESTAMP: ${TIMESTAMP}" \
  -H "KC-API-PASSPHRASE: ${ENCRYPTED_PASSPHRASE}" \
  -H "KC-API-KEY-VERSION: ${API_KEY_VERSION}" \
  -H "Content-Type: application/json" \
  -H "User-Agent: kucoin-assets/1.0.0 (Skill)" \
  -d "${BODY}"
```

### Python Example

```python
import hashlib
import hmac
import base64
import time
import requests

# ----- Configuration -----
API_KEY = "your-api-key"
API_SECRET = "your-api-secret"
API_PASSPHRASE = "your-api-passphrase"
API_KEY_VERSION = "2"
BASE_URL = "https://api.kucoin.com"


def create_signature(secret: str, prehash: str) -> str:
    """Compute Base64(HMAC-SHA256(prehash, secret))."""
    mac = hmac.new(
        secret.encode("utf-8"),
        prehash.encode("utf-8"),
        hashlib.sha256,
    )
    return base64.b64encode(mac.digest()).decode("utf-8")


def encrypt_passphrase(passphrase: str, secret: str) -> str:
    """Encrypt the passphrase for API Key Version 2."""
    mac = hmac.new(
        secret.encode("utf-8"),
        passphrase.encode("utf-8"),
        hashlib.sha256,
    )
    return base64.b64encode(mac.digest()).decode("utf-8")


def build_headers(method: str, endpoint: str, body: str = "") -> dict:
    """Build authentication headers for a KuCoin API request."""
    timestamp = str(int(time.time() * 1000))
    prehash = timestamp + method.upper() + endpoint + body

    signature = create_signature(API_SECRET, prehash)

    # Version 2: encrypt passphrase; Version 1: use plain passphrase
    if API_KEY_VERSION == "2":
        passphrase = encrypt_passphrase(API_PASSPHRASE, API_SECRET)
    else:
        passphrase = API_PASSPHRASE

    return {
        "KC-API-KEY": API_KEY,
        "KC-API-SIGN": signature,
        "KC-API-TIMESTAMP": timestamp,
        "KC-API-PASSPHRASE": passphrase,
        "KC-API-KEY-VERSION": API_KEY_VERSION,
        "Content-Type": "application/json",
        "User-Agent": "kucoin-assets/1.0.0 (Skill)",
    }


# ----- GET example: fetch account list -----
endpoint = "/api/v1/accounts?currency=BTC"
headers = build_headers("GET", endpoint)
response = requests.get(BASE_URL + endpoint, headers=headers)
print("GET /api/v1/accounts:", response.json())


# ----- POST example: create a withdrawal -----
endpoint = "/api/v3/withdrawals"
body = '{"currency":"USDT","address":"0xABC123...","amount":"50","chain":"eth"}'
headers = build_headers("POST", endpoint, body)
response = requests.post(BASE_URL + endpoint, headers=headers, data=body)
print("POST /api/v3/withdrawals:", response.json())
```

## Error Handling

If authentication fails, KuCoin returns one of the following error codes:

| Code | Message | Description |
|------|---------|-------------|
| `400001` | Any of KC-API-KEY, KC-API-SIGN, KC-API-TIMESTAMP, KC-API-PASSPHRASE is missing in your request header. | A required authentication header is missing. |
| `400002` | KC-API-TIMESTAMP Invalid. | The timestamp is missing or not a valid number. |
| `400003` | KC-API-KEY not exists. | The API key does not exist or has been deleted. |
| `400004` | KC-API-PASSPHRASE error. | The passphrase is incorrect or not properly encrypted. |
| `400005` | Signature not correct. | The HMAC-SHA256 signature does not match. Verify the prehash string construction. |
| `400006` | The requested ip address is not in the api whitelist. | The request IP is not in the API key's IP whitelist. |
| `400007` | Access Denied. | The API key does not have permission for this endpoint. |
| `429000` | Too Many Requests. | Rate limit exceeded. Implement backoff and retry logic. |

## Security Notes

1. **Never expose** your API Secret in client-side code, logs, or version control.
2. **Rotate keys** periodically and revoke unused API keys.
3. **Use IP whitelisting** to restrict API key access to known IP addresses.
4. **Use API Key Version 2** to ensure the passphrase is encrypted in transit.
5. **Validate timestamps** to prevent replay attacks (server allows a 5-second window).
