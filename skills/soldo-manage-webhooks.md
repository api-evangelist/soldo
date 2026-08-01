---
name: Manage Soldo webhook subscriptions
description: Subscribe to, list, and remove webhook event subscriptions so an integration receives real-time Soldo events.
api: openapi/soldo-business-api-v20-openapi-original.json
operations: [webhook-subscription-search, webhook-subscription-add, webhook-subscription-delete]
---

# Manage Soldo webhook subscriptions

Wire an integration to Soldo's 81 event types (transactions, cards, wallets, users, expense reviews, etc.).

## Preconditions
- Valid bearer token with `webhook_subscription_read` and `webhook_subscription_write` scopes.
- A public HTTPS endpoint able to verify Soldo's signature on inbound callbacks.

## Steps
1. **`webhook-subscription-search`** — list existing subscriptions to avoid duplicates.
2. **`webhook-subscription-add`** — register your callback URL for the desired event(s). Advanced auth required.
3. **`webhook-subscription-delete`** — remove a subscription when no longer needed.

## Handling inbound events
- Soldo POSTs each event to your URL with `X-Soldo-Fingerprint`, `X-Soldo-Fingerprint-Order`, and `X-Soldo-Fingerprint-Signature` headers.
- Verify `X-Soldo-Fingerprint-Signature` against Soldo's public key using SHA512withRSA before trusting the payload (see the Java `checkSignatureByPublicKey` sample). Reject unverified events.
- Event catalog: `asyncapi/soldo-webhooks.yml`.

## Rules
- Rate limits and the `{ error_code, message }` error envelope apply as elsewhere.
