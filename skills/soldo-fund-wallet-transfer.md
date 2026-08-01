---
name: Move funds between Soldo wallets
description: Locate wallets and perform an internal transfer to top up a spending wallet.
api: openapi/soldo-business-api-v20-openapi-original.json
operations: [wallet-search, wallet-get, wallet-internal-transfer]
---

# Move funds between Soldo wallets

Top up a wallet by transferring funds from another wallet in the same account.

## Preconditions
- Valid bearer token with `wallet_read` and `wallet_write` scopes.
- `wallet-internal-transfer` is a money-movement operation and **requires advanced authentication** — compute `X-Soldo-Fingerprint` (SHA-512, lowercase, of the endpoint's ordered fields + fingerprint token) and `X-Soldo-Fingerprint-Signature` (SHA512withRSA, Base64).

## Steps
1. **`wallet-search`** — list wallets; identify source and destination wallet `id`s. They must share a `currency_code`.
2. **`wallet-get`** — confirm the source wallet's `available_amount` covers the transfer.
3. **`wallet-internal-transfer`** — execute the transfer. Advanced auth required.

## Rules
- Never retry a transfer blindly on a network error — there is no idempotency key. Re-read wallet balances with `wallet-get` to confirm whether the prior attempt applied.
- A `400 INVALID_FINGERPRINT` / `INVALID_FINGERPRINT_SIGNATURE` means the signing inputs or order were wrong; recompute per the endpoint reference.
- All money-movement operations are audited.
