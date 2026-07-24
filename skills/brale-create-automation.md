---
name: Create a deposit automation (auto-mint on fiat deposit)
description: Provision a virtual U.S. account/routing number that auto-mints stablecoins to a wallet when fiat is deposited.
api: openapi/brale-openapi-original.yml
operations: [create_automation, get_automations, get_automation, update_automation_status]
---

# Create a deposit automation (Brale)

Give a customer a unique bank account/routing number that mints stablecoins automatically when fiat arrives.

## Auth
OAuth2 client-credentials bearer token. Scopes: `automations:read`, `automations:write`, plus `addresses:write` for the destination.

## Steps
1. **create_automation** — `POST /accounts/{account_id}/automations` specifying the destination wallet (`address_id`) and target stablecoin (`value_type` + `transfer_type`/chain). Brale returns the automation with its virtual account/routing number. Save the `automation_id`.
2. **get_automations** — `GET /accounts/{account_id}/automations` to list all automations for the account.
3. **get_automation** — `GET /accounts/{account_id}/automations/{automation_id}` to read a single automation's details/status.
4. **update_automation_status** — `PATCH /accounts/{account_id}/automations/{automation_id}` to pause/resume; archiving permanently stops deposit processing and closes the virtual account.

## Rules
- Fiat deposited to the virtual account auto-mints to the preset destination — verify the destination `address_id` before activating.
- Automations are billed per virtual account (see https://brale.xyz/pricing).
