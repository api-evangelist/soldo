---
name: Authenticate with the Soldo Business API
description: Obtain an OAuth2 access token via the Client Credentials grant and confirm the identity and scopes of the token owner.
api: openapi/soldo-business-api-v20-openapi-original.json
operations: [authorize, who-am-i]
---

# Authenticate with the Soldo Business API

Use this to get a bearer token before calling any other Soldo operation.

## Preconditions
- A `client_id` and `client_secret` generated in the Soldo web app (Integrations > Business API).
- Base URL: `https://api.soldo.com` (production) or `https://api-demo.soldocloud.net` (sandbox).

## Steps
1. **`authorize`** — POST `client_id` and `client_secret` as `application/x-www-form-urlencoded` to `https://api.soldo.com/oauth/authorize`. Read `access_token` from the JSON response. Tokens expire after 7200 seconds (2 hours).
2. Send every subsequent request with header `Authorization: Bearer {access_token}`.
3. **`who-am-i`** — GET the token-owner info to confirm the token is valid and to read the scopes enabled on the application.

## Rules
- Cache the token and refresh it before the 2-hour expiry, or on a `401` with `error: invalid_token` (see `errors/soldo-error-codes.yml`).
- Sensitive/money-movement operations additionally require advanced-auth request signing (`X-Soldo-Fingerprint` + `X-Soldo-Fingerprint-Signature`) — see `conventions/soldo-conventions.yml`.
- Respect rate limits: 600/min, 20/sec; back off on `429`.
