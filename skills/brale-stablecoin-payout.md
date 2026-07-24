---
name: Send a stablecoin payout to an external wallet
description: Check a custodial balance, register an external destination, and pay out stablecoin to any onchain wallet.
api: openapi/brale-openapi-original.yml
operations: [list_account_addresses, get_address_token_balance, create_external_address, create_transfer, get_transfer]
---

# Send a stablecoin payout (Brale)

Pay stablecoin from a custodial address to an external wallet.

## Auth
OAuth2 client-credentials bearer token. Scopes: `addresses:read`, `addresses:write`, `transfers:read`, `transfers:write`.

## Steps
1. **list_account_addresses** — `GET /accounts/{account_id}/addresses` to find your internal (custodial) `address_id`.
2. **get_address_token_balance** — `GET /accounts/{account_id}/addresses/{address_id}/balance` (pass `transfer_type` + `value_type` as query params) to confirm sufficient balance.
3. **create_external_address** — `POST /accounts/{account_id}/addresses/external` to register the destination onchain wallet (`CreateExternalAddressRequest` blockchain variant). Save the external `address_id`.
4. **create_transfer** — `POST /accounts/{account_id}/transfers` with the custodial source `address_id` and the external destination `address_id` (`value_type` e.g. `SBC`, `transfer_type` e.g. `base`). Set a unique `Idempotency-Key`.
5. **get_transfer** — poll `GET /accounts/{account_id}/transfers/{transfer_id}` for finality.

## Rules
- Confirm the destination chain (`transfer_type`) matches a supported network (see conventions/brale-conventions.yml).
- Never retry a failed payout without the original `Idempotency-Key`.
