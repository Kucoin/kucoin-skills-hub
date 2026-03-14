# KuCoin Authentication

KuCoin uses a 3-credential authentication system: API Key, API Secret, and Passphrase.

## Base URLs

| Environment | URL |
|-------------|-----|
| Spot/Margin | https://api.kucoin.com |
| Futures | https://api-futures.kucoin.com |

## Required Headers

* `KC-API-KEY`: Your API Key
* `KC-API-SIGN`: HMAC-SHA256 signature (Base64 encoded)
* `KC-API-TIMESTAMP`: Current Unix time in milliseconds
* `KC-API-PASSPHRASE`: HMAC-SHA256 signed passphrase (Base64 encoded)
* `KC-API-KEY-VERSION`: `3`
* `Content-Type`: `application/json` (for POST/PUT/DELETE with body)
* `User-Agent`: `kucoin-spot/1.0.0 (Skill)`

## Signing Process

### Step 1: Build String to Sign

Concatenate: `timestamp + method + requestEndpoint + body`

| Component | Description | Example |
|-----------|-------------|---------|
| `timestamp` | Unix time in milliseconds (same as KC-API-TIMESTAMP header) | `1234567890123` |
| `method` | HTTP method in UPPERCASE | `GET`, `POST`, `DELETE` |
| `requestEndpoint` | URL path with query string | `/api/v1/accounts?currency=BTC` |
| `body` | JSON body string for POST/PUT, empty string for GET/DELETE without body | `{"symbol":"BTC-USDT","side":"buy"}` |

**Examples:**

For a GET request:
```
1234567890123GET/api/v1/accounts?currency=BTC
```

For a POST request:
```
1234567890123POST/api/v1/hf/orders{"symbol":"BTC-USDT","side":"buy","type":"limit","price":"30000","size":"0.001"}
```

### Step 2: Generate Signature

HMAC-SHA256 of the string using secretKey, then Base64 encode:

```bash
echo -n "1234567890123GET/api/v1/accounts?currency=BTC" | \
  openssl dgst -sha256 -hmac "your_secret_key" -binary | base64
```

### Step 3: Sign Passphrase

HMAC-SHA256 of the passphrase using secretKey, then Base64 encode. Set `KC-API-KEY-VERSION` to `"3"`.

```bash
echo -n "your_passphrase" | \
  openssl dgst -sha256 -hmac "your_secret_key" -binary | base64
```

### Step 4: Add Headers

Include all `KC-API-*` headers in the request.

### Step 5: Add User Agent Header

Include `User-Agent: kucoin-spot/1.0.0 (Skill)`

#### Complete Examples

**GET Request (Query Accounts):**

```bash
#!/bin/bash
API_KEY="your-api-key"
SECRET_KEY="your-secret-key"
PASSPHRASE="your-passphrase"
BASE_URL="https://api.kucoin.com"

# Request details
METHOD="GET"
ENDPOINT="/api/v1/accounts?currency=BTC"
BODY=""
TIMESTAMP=$(date +%s000)

# Build string to sign
STRING_TO_SIGN="${TIMESTAMP}${METHOD}${ENDPOINT}${BODY}"

# Generate signature
SIGNATURE=$(echo -n "${STRING_TO_SIGN}" | openssl dgst -sha256 -hmac "${SECRET_KEY}" -binary | base64)

# Sign passphrase
SIGNED_PASSPHRASE=$(echo -n "${PASSPHRASE}" | openssl dgst -sha256 -hmac "${SECRET_KEY}" -binary | base64)

# Send request
curl -X GET "${BASE_URL}${ENDPOINT}" \
  -H "KC-API-KEY: ${API_KEY}" \
  -H "KC-API-SIGN: ${SIGNATURE}" \
  -H "KC-API-TIMESTAMP: ${TIMESTAMP}" \
  -H "KC-API-PASSPHRASE: ${SIGNED_PASSPHRASE}" \
  -H "KC-API-KEY-VERSION: 3" \
  -H "Content-Type: application/json" \
  -H "User-Agent: kucoin-spot/1.0.0 (Skill)"
```

**POST Request (Place Order):**

```bash
#!/bin/bash
API_KEY="your-api-key"
SECRET_KEY="your-secret-key"
PASSPHRASE="your-passphrase"
BASE_URL="https://api.kucoin.com"

# Request details
METHOD="POST"
ENDPOINT="/api/v1/hf/orders"
BODY='{"clientOid":"agent-273edcef-532d-46ba-9e0c-14a8da03ca9b","symbol":"BTC-USDT","side":"buy","type":"limit","price":"30000","size":"0.001"}'
TIMESTAMP=$(date +%s000)

# Build string to sign
STRING_TO_SIGN="${TIMESTAMP}${METHOD}${ENDPOINT}${BODY}"

# Generate signature
SIGNATURE=$(echo -n "${STRING_TO_SIGN}" | openssl dgst -sha256 -hmac "${SECRET_KEY}" -binary | base64)

# Sign passphrase
SIGNED_PASSPHRASE=$(echo -n "${PASSPHRASE}" | openssl dgst -sha256 -hmac "${SECRET_KEY}" -binary | base64)

# Send request
curl -X POST "${BASE_URL}${ENDPOINT}" \
  -H "KC-API-KEY: ${API_KEY}" \
  -H "KC-API-SIGN: ${SIGNATURE}" \
  -H "KC-API-TIMESTAMP: ${TIMESTAMP}" \
  -H "KC-API-PASSPHRASE: ${SIGNED_PASSPHRASE}" \
  -H "KC-API-KEY-VERSION: 3" \
  -H "Content-Type: application/json" \
  -H "User-Agent: kucoin-spot/1.0.0 (Skill)" \
  -d "${BODY}"
```

## Security Notes

* Never share your secret key or passphrase
* Use IP whitelist in KuCoin API settings
* Enable only required permissions
