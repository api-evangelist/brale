---
name: Onramp fiat to a stablecoin
description: Create a customer account, provision a custodial address, and move fiat into stablecoin via a Brale transfer.
api: openapi/brale-openapi-original.yml
operations: [create_account, create_internal_address, create_transfer, get_transfer]
---

# Onramp fiat to a stablecoin (Brale)

Move USD into a stablecoin balance for a customer.

## Auth
Get an OAuth2 client-credentials token from `https://auth.brale.xyz/oauth2/token`, then send `Authorization: Bearer {access_token}`. You need the `accounts:write`, `addresses:write`, and `transfers:write` scopes.

## Steps
1. **create_account** — `POST /accounts` with KYB/KYC details (`CreateManagedAccountRequest`: `beneficial_owners`, `business_controller`, `EndUserTosAttestation`). Save the returned `account_id` (a KSUID).
2. **create_internal_address** — `POST /accounts/{account_id}/addresses/internal` to provision a custodial address that will hold the stablecoin. Save the `address_id`.
3. **create_transfer** — `POST /accounts/{account_id}/transfers` with a fiat source (e.g. `transfer_type: wire` or `ach_debit`, `value_type: USD`) and a stablecoin destination (`destination.address_id`, `value_type: SBC/USDC`, `transfer_type: base`). Send a unique `Idempotency-Key` header so retries don't double-mint.
4. **get_transfer** — `GET /accounts/{account_id}/transfers/{transfer_id}` to poll status until settled. (Subscribe to webhooks instead of polling for production.)

## Rules
- All IDs are KSUIDs (26-char), not UUIDs.
- Errors are JSON:API: inspect `errors[].code` / `errors[].source.pointer` (see errors/brale-problem-types.yml).
- Always set `Idempotency-Key` on `create_transfer` (see conventions/brale-conventions.yml).
