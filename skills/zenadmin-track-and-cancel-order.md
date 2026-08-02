---
name: track-and-cancel-order
description: List, inspect, and cancel ZenAdmin hardware orders.
api: ZenAdmin External API
base_url: https://console.zenadmin.ai/api/external
auth: x-api-key header (per-key API key)
operations:
  - listOrders
  - getOrder
  - cancelOrder
---

# Track and cancel ZenAdmin orders

## Auth
Send `x-api-key: <your-key>` on every request.

## Steps
1. **List orders** — `GET /orders` (`listOrders`), filtering with `status`, `from`, `to`, and paginating with `page`/`limit`. Read the `meta_data` object (`total_items`, `page_no`, `items_on_page`) to page through results.
2. **Inspect one** — `GET /orders/{rfq_id}` (`getOrder`) and read `status`.
3. **Cancel** — `POST /orders/{rfq_id}/cancel` (`cancelOrder`). Confirm by re-reading the order.

## Rules
- A 404 means the `rfq_id` is unknown — do not retry blindly.
- Errors return `{"success": false, "message": "..."}`. Check `success` before trusting `data`.
- Respect the 60 req/min (burst 120) per-key rate limit when paging large order lists.
