---
name: manage-webhooks
description: Subscribe to ZenAdmin events, inspect deliveries, and replay failures.
api: ZenAdmin External API
base_url: https://console.zenadmin.ai/api/external
auth: x-api-key header (per-key API key)
operations:
  - createWebhookSubscription
  - listWebhookSubscriptions
  - listWebhookDeliveries
  - redeliverWebhookDelivery
---

# Manage ZenAdmin webhooks

## Auth
Send `x-api-key: <your-key>` on every request.

## Steps
1. **Discover events** — `GET /me` (`getApiContext`) returns `supported_events` for your key. Documented events: `rfq.created`, `order.status_changed`.
2. **Subscribe** — `POST /webhooks/subscriptions` (`createWebhookSubscription`) with a target `url` and an `events` array.
3. **Verify each callback** — recompute `HMAC-SHA256(sha256(secret), \`${X-Zenadmin-Timestamp}.${rawBody}\`)` and compare to the `X-Zenadmin-Signature` header (`sha256=<hex>`). Reject on mismatch or a stale timestamp.
4. **Audit** — `GET /webhooks/deliveries` (`listWebhookDeliveries`); replay a failed one with `POST /webhooks/deliveries/{id}/redeliver` (`redeliverWebhookDelivery`).
5. **Manage** — update via `PATCH /webhooks/subscriptions/{id}`, remove via `DELETE /webhooks/subscriptions/{id}`.

## Rules
- Always verify the HMAC signature before acting on a payload.
- Errors return `{"success": false, "message": "..."}`.
