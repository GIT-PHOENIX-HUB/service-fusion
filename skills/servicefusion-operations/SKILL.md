---
name: servicefusion-operations
description: This skill should be used when the user asks to "check service fusion", "look up a customer", "create an estimate", "check the schedule", "morning briefing", "what jobs are open", "who's on call", "pricebook", "update materials", "create invoice", "missed calls", "campaign ROI", "Rexel pricing", "build a service", "contract", "proposal", or any Service Fusion / SF operations task. Provides complete operational control over Phoenix Electric's Service Fusion tenant.
---

# Service Fusion Operations

Complete operational skill for Phoenix Electric's Service Fusion tenant. Covers CRM, jobs, dispatch, pricebook, accounting, telecom, memberships, marketing, and settings — 47 tools total.

## Authentication

Authenticate via Azure Key Vault (`PhoenixaAiVault`). Requires `az login` on the machine. The MCP server pulls credentials automatically:
- `SERVICEFUSION-CORE-CLIENT-ID` / `PhoenixAiCommandClientId`
- `SERVICEFUSION-CORE-SECRET` / `PhoenixAiCommandSecret`
- `SERVICEFUSION-CORE-APP-KEY` / `PhoenixAiCommandAppKey`
- `SERVICEFUSION-TENANT-ID` / `PhoenixAiCommandTenantId`

App registration: "PHOENIX AI Command" in Azure AD.

## Tool Conventions

- All tools available with `servicefusion_*` or `sf_*` prefix (aliases)
- **Read operations:** No approval needed — call freely
- **Write operations:** Require `SF_APPROVAL_TOKEN` env var or `ALLOW_SF_WRITES=true`
- Always confirm with user before write operations
- Paginated endpoints support `page` and `pageSize` parameters

## Tool Categories (47 Tools)

| Category | Count | Key Tools |
|----------|-------|-----------|
| CRM | 7 | `sf_list_customers`, `sf_search_customers`, `sf_create_customer`, `sf_list_locations`, `sf_list_bookings`, `sf_create_booking` |
| Jobs | 8 | `sf_list_jobs`, `sf_get_job`, `sf_create_job`, `sf_cancel_job`, `sf_list_appointments`, `sf_reschedule_appointment`, `sf_get_daily_job_summary` |
| Dispatch | 6 | `sf_get_capacity`, `sf_list_technicians`, `sf_get_on_call_technician`, `sf_list_technician_shifts`, `sf_list_zones` |
| Pricebook | 7 | `sf_list_services`, `sf_list_materials`, `sf_create_material`, `sf_update_material`, `sf_compare_prices`, `sf_list_equipment`, `sf_list_categories` |
| Accounting | 6 | `sf_list_invoices`, `sf_get_invoice`, `sf_list_estimates`, `sf_create_estimate`, `sf_sell_estimate`, `sf_list_payments` |
| Telecom | 4 | `sf_list_calls`, `sf_get_call`, `sf_get_missed_calls`, `sf_get_calls_with_recordings` |
| Memberships | 3 | `sf_list_membership_types`, `sf_list_customer_memberships`, `sf_list_recurring_services` |
| Marketing | 3 | `sf_list_campaigns`, `sf_list_campaign_categories`, `sf_list_campaign_costs` |
| Settings | 3 | `sf_list_employees`, `sf_list_business_units`, `sf_list_tag_types` |

## Common Workflows

### Morning Briefing
1. `sf_get_daily_job_summary` with today's date
2. `sf_get_missed_calls` since last business day
3. `sf_list_estimates` with status "Open"
4. `sf_get_capacity` for today
5. `sf_get_on_call_technician`
6. Format as executive summary table

### Estimate Creation
1. `sf_search_customers` to find customer
2. `sf_list_jobs` for customer's active jobs (or `sf_create_job`)
3. `sf_list_services` + `sf_list_materials` to browse pricebook
4. Build line items with quantities and pricing
5. `sf_create_estimate` with assembled data
6. Optionally generate formatted document

### Pricebook Update (Rexel Sync)
1. Gather Rexel invoice data (manual input or file)
2. `sf_compare_prices` to find changes vs current pricebook
3. Review increases/decreases/new items
4. `sf_update_material` or `sf_create_material` for approved changes

### Contract/Proposal Generation
1. `sf_get_customer` for customer details
2. `sf_get_job` for scope of work
3. `sf_list_services` + `sf_list_materials` for pricing
4. Compose document with Phoenix Electric branding, terms, scope, pricing
5. Write to file as markdown (convertible to PDF)

## Additional Resources

### Reference Files
- **`references/api-reference.md`** — All 47 tools with input schemas and response examples
- **`references/workflows.md`** — Detailed step-by-step operational workflows
- **`references/financials.md`** — Invoice lifecycle, payment tracking, estimate pipeline, pricebook tiers
- **`references/rexel-integration.md`** — Rexel invoice-to-pricebook workflow, vendor cost analysis, markup rules
- **`references/browser-fallback.md`** — Chrome automation for API-limited operations (reports, PDF export, complex scheduling)
- **`references/future-gateway.md`** — Architecture for Studio/Gateway features: crons, GPS, auto-logging, scheduled briefings

## Important Notes

- **Phoenix Electric is an ELECTRICAL company, NOT HVAC**
- SF API paths include tenant ID: `/crm/v2/tenant/{tenantId}/customers`
- Production API: `https://api.servicefusion.com`
- Auth endpoint: `https://auth.servicefusion.com/connect/token`
- OAuth grant type: `client_credentials`
- Token cached with 60-second expiry buffer
- Retries on 429 and 5xx with exponential backoff
