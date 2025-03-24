
### Token Issuance
**Endpoint:**
```
POST /client/auth/token
```
**Request:**
```json
{
  "client_id": "agent-123",
  "client_secret": "secure-secret"
}
```
**Response:**
```json
{
  "access_token": "mmcp-token-xyz",
  "expires_in": 3600
}
```

## Billing & Credit System
### Account balance
**Endpoint:**
```
GET /client/billing
```
**Response:**
```json
{
  "balance": 0.00,
  "currency": "USD",
}
```

### Credit Deposit
**Endpoint:**
```
POST /client/billing
```
**Response:**
```json
{
  "balance": 0.00,
  "currency": "USD",
  "payment_url": "https://checkout.stripe.com/..."
}
```


## API Discovery & Registration
MCP servers must register with MMCP to be discoverable and enforce monetization rules.

### API Registration
**Endpoint:**
```
POST /server/register
```
**Request:**
```json
{
  "api_url": "https://mcp-api1.com",
  "pricing": {
    "request": 0.01,
    "token": 0.00001
  },
  "access_token": "server-auth-token"
}
```
**Response:**
```json
{
  "status": "registered",
  "api_id": "api-xyz"
}
```

TODO: Expire / grandfather / custom pricing for existing users.

## Client Feedback
To improve response quality tracking, clients can provide feedback on individual requests.

### Submit Feedback
**Endpoint:**
```
POST /client/feedback
```
**Request:**
```json
{
  "request_id": "<uuid>",
  "discarded": true
}
```
**Response:**
HTTP 204