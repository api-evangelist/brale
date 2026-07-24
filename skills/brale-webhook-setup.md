---
name: Subscribe to and debug Brale webhooks
description: Discover event types, create a webhook subscription, and inspect or replay deliveries.
api: openapi/brale-openapi-original.yml
operations: [list_webhook_event_types, create_webhook_subscription, get_webhook_subscription, list_webhook_deliveries, replay_webhook_event]
---

# Subscribe to and debug Brale webhooks

Get notified of transfer/account events instead of polling.

## Auth
OAuth2 client-credentials bearer token. Scopes: `webhooks:read`, `webhooks:write`.

## Steps
1. **list_webhook_event_types** — `GET /accounts/{account_id}/webhooks/event_types` to discover the event types supported for the account.
2. **create_webhook_subscription** — `POST /accounts/{account_id}/webhooks` with your HTTPS endpoint URL and the subscribed event types. Save the `subscription_id`.
3. **get_webhook_subscription** — `GET /accounts/{account_id}/webhooks/{subscription_id}` to confirm the subscription.
4. **list_webhook_deliveries** — `GET /accounts/{account_id}/webhooks/deliveries` to debug delivery status, retries, and failures.
5. **replay_webhook_event** — `POST /accounts/{account_id}/webhooks/events/{event_id}/resend` to re-deliver an event after fixing your endpoint.

## Rules
- Endpoints must be HTTPS; Brale retains delivery history and retries failures.
- Deleting (archiving) a subscription stops future deliveries but retains history.
