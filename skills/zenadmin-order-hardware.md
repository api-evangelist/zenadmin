---
name: order-hardware
description: Browse the ZenAdmin device catalog and place an idempotent hardware order.
api: ZenAdmin External API
base_url: https://console.zenadmin.ai/api/external
auth: x-api-key header (per-key API key)
operations:
  - listCatalogCountries
  - listCatalogDevices
  - createOrder
  - getOrder
---

# Order hardware through ZenAdmin

Place a hardware order against the ZenAdmin catalog.

## Auth
Send `x-api-key: <your-key>` on every request (or `Authorization: Bearer <your-key>`). Keys are shown once at creation.

## Steps
1. **Resolve the destination country** — `GET /catalog/countries` (`listCatalogCountries`) and pick the `country_id` for the delivery location.
2. **Find a device** — `GET /catalog/devices?country_id=<id>&search=<term>` (`listCatalogDevices`), paginating with `page`/`limit`. Note the `product_configuration_id`. Use `GET /catalog/devices/{product_configuration_id}` (`getCatalogDevice`) for full details.
3. **Create the order idempotently** — `POST /orders` (`createOrder`) with a stable `external_request_id` you generate. Reusing the same `external_request_id` returns the original order instead of creating a duplicate — safe to retry on timeout.
4. **Confirm** — read the returned `rfq_id`, then `GET /orders/{rfq_id}` (`getOrder`) to check `status`.

## Rules
- Rate limit: 60 req/min (burst 120) per key — back off on repeated 401/error envelopes.
- Errors return `{"success": false, "message": "..."}` (not RFC 9457). Treat `success: false` as failure regardless of parsing.
- Never send the same order twice without the same `external_request_id`.
