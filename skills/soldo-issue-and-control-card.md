---
name: Issue and control a Soldo card
description: Create a company card on a wallet, then apply spending-control rules and assignees.
api: openapi/soldo-business-api-v20-openapi-original.json
operations: [wallet-search, card-add, card-rules-set, card-update]
---

# Issue and control a Soldo card

Provision a new card and constrain how it can be spent.

## Preconditions
- Valid bearer token (see `soldo-authenticate.md`) with `card_write` and `wallet_read` scopes.
- `card-add` is an advanced-auth operation — compute `X-Soldo-Fingerprint` and `X-Soldo-Fingerprint-Signature` per the endpoint's fingerprint order (`conventions/soldo-conventions.yml`).

## Steps
1. **`wallet-search`** — find the target `Wallet`; note its `id` and `currency_code`. A new card inherits the wallet's currency.
2. **`card-add`** — create the `Card` on that wallet. Advanced auth required.
3. **`card-rules-set`** — apply `CardRules` (spending limits, allowed countries, merchant categories, cashpoint rules) to constrain the card.
4. **`card-update`** — add or remove assignees (the users allowed to use the card).

## Rules
- Deleting a wallet with a non-zero balance fails with `WALLET_BALANCE_GREATER_THAN_ZERO`; transfer funds first.
- Paginate `wallet-search` with `p`/`s` (max page size 50).
- All write operations are audited; there is no idempotency key, so avoid blind retries on non-idempotent POSTs — re-check state with a search/get first.
