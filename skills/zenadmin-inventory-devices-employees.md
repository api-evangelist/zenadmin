---
name: inventory-devices-employees
description: Enumerate managed ZenAdmin devices and employees for reporting.
api: ZenAdmin External API
base_url: https://console.zenadmin.ai/api/external
auth: x-api-key header (per-key API key)
operations:
  - getApiContext
  - listDevices
  - getDevice
  - listEmployees
  - getEmployee
---

# Inventory ZenAdmin devices and employees

## Auth
Send `x-api-key: <your-key>` on every request.

## Steps
1. **Confirm context** — `GET /me` (`getApiContext`) to read `company_id` and `api_version`.
2. **List devices** — `GET /devices?page=0&limit=25` (`listDevices`); page through using `meta_data.total_items` and `meta_data.page_no`. Fetch detail with `GET /devices/{id}` (`getDevice`).
3. **List employees** — `GET /employees` (`listEmployees`), filtering with `search`, `status`, `department_id`. Fetch detail with `GET /employees/{id}` (`getEmployee`).

## Rules
- Pagination is `page`/`limit`; the response envelope carries `data` plus `meta_data`.
- Respect the 60 req/min (burst 120) per-key rate limit when enumerating large fleets.
- Errors return `{"success": false, "message": "..."}`; check `success` first.
