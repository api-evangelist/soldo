---
name: Reconcile Soldo transactions and attach receipts
description: Search and read transactions, then upload and confirm a receipt document against a transaction for expense reconciliation.
api: openapi/soldo-business-api-v20-openapi-original.json
operations: [transaction-search, transaction-get, document-upload, document-upload-confirm]
---

# Reconcile Soldo transactions and attach receipts

Pull spend data and attach supporting documents for accounting.

## Preconditions
- Valid bearer token with `transaction_read` scope (and write scope to attach documents).
- `transaction-search` is an advanced-auth operation — sign the request per its fingerprint order.

## Steps
1. **`transaction-search`** — filter by `fromDate`/`toDate`, `dateType`, `category`, `status`. Advanced auth required. Paginate with `p`/`s`.
2. **`transaction-get`** — read a single transaction by `id`. A non-null `reimbursement_expense_report_id` means the payment reimbursed an expense report.
3. **`document-upload`** — request a temporary pre-signed upload URL for the receipt.
4. Upload the file bytes to the returned URL.
5. **`document-upload-confirm`** — confirm the upload so Soldo attaches the document.

## Rules
- Dates are inclusive of `fromDate` and exclusive of `toDate`.
- Error envelope is `{ error_code, message }` (not RFC 9457) — see `errors/soldo-error-codes.yml`.
- Rate limits: 600/min, 20/sec; honor `x-ratelimit-remaining-*` headers.
