# Service Fusion API Reference — All 47 Tools

> Source of truth: `~/GitHub/phoenix-ai-core-staging/packages/servicefusion-mcp/src/tools/index.ts`
> MCP server prefix: `servicefusion_*` (aliases: `sf_*`)

---

## CRM Tools (7)

### `servicefusion_list_customers`
List customers with optional filters.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `active` | boolean | No | — | Filter by active status |
| `name` | string | No | — | Filter by name |
| `modifiedOnOrAfter` | string | No | — | ISO date filter |
| `page` | number | No | — | Page number |
| `pageSize` | number | No | 50 | Results per page |

**Approval:** Read (none needed)
**Response:** `PaginatedResponse<SFCustomer>` — `{ data: SFCustomer[], totalCount, page, pageSize }`

---

### `servicefusion_get_customer`
Get a specific customer by ID.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `customerId` | number | Yes | Customer ID |

**Approval:** Read
**Response:** `SFCustomer` object

---

### `servicefusion_create_customer`
Create a new customer.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Customer name |
| `type` | `"Residential"` \| `"Commercial"` | Yes | Customer type |
| `address` | object | No | `{ street, city, state, zip }` |
| `contacts` | array | No | `[{ type: "Phone"|"Email"|"MobilePhone", value }]` |

**Approval:** Write (confirmation required)
**Response:** Created `SFCustomer` object

---

### `servicefusion_search_customers`
Search customers by name, phone, or email.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | Yes | — | Search term |
| `limit` | number | No | 10 | Max results |

**Approval:** Read
**Response:** `PaginatedResponse<SFCustomer>`

---

### `servicefusion_list_locations`
List service locations.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `customerId` | number | No | Filter by customer |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated locations

---

### `servicefusion_list_bookings`
List booking requests.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `status` | `"Pending"` \| `"Scheduled"` \| `"Canceled"` | No | Filter by status |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated bookings

---

### `servicefusion_create_booking`
Create a new booking/appointment request.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `customerId` | number | No | Existing customer ID |
| `name` | string | No | New customer name |
| `phone` | string | No | Contact phone |
| `start` | string | Yes | ISO datetime for appointment |
| `summary` | string | No | Booking description |
| `businessUnitId` | number | No | Business unit |

**Approval:** Write (confirmation required)
**Response:** Created booking object

---

## Jobs Tools (8)

### `servicefusion_list_jobs`
List jobs with optional filters.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `status` | enum | No | — | `Pending`, `Scheduled`, `Dispatched`, `Working`, `Hold`, `Completed`, `Canceled` |
| `customerId` | number | No | — | Filter by customer |
| `technicianId` | number | No | — | Filter by technician |
| `createdOnOrAfter` | string | No | — | ISO date |
| `modifiedOnOrAfter` | string | No | — | ISO date |
| `page` | number | No | — | Page number |
| `pageSize` | number | No | 50 | Results per page |

**Approval:** Read
**Response:** `PaginatedResponse<SFJob>`

---

### `servicefusion_get_job`
Get detailed information about a specific job.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jobId` | number | Yes | Job ID |

**Approval:** Read
**Response:** `SFJob` object

---

### `servicefusion_create_job`
Create a new job.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `customerId` | number | Yes | — | Customer ID |
| `locationId` | number | Yes | — | Location ID |
| `jobTypeId` | number | Yes | — | Job type (use `list_job_types` to find) |
| `priority` | enum | No | `Normal` | `Low`, `Normal`, `High`, `Urgent` |
| `summary` | string | No | — | Job description |

**Approval:** Write (confirmation required)
**Response:** Created `SFJob` object

---

### `servicefusion_cancel_job`
Cancel a job.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jobId` | number | Yes | Job ID |
| `reason` | string | No | Cancellation reason |

**Approval:** Write (confirmation required)
**Response:** Updated job object

---

### `servicefusion_list_appointments`
List appointments.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jobId` | number | No | Filter by job |
| `technicianId` | number | No | Filter by technician |
| `startsOnOrAfter` | string | No | ISO datetime |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated appointments

---

### `servicefusion_reschedule_appointment`
Reschedule an appointment.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `appointmentId` | number | Yes | Appointment ID |
| `start` | string | Yes | New start time (ISO) |
| `end` | string | Yes | New end time (ISO) |
| `technicianId` | number | No | Reassign to different tech |

**Approval:** Write (confirmation required)
**Response:** Updated appointment object

---

### `servicefusion_list_job_types`
List available job types.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `active` | boolean | No | Filter by active status |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated job types

---

### `servicefusion_get_daily_job_summary`
Get a summary of jobs for a specific date with counts by status and priority.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `date` | string | Yes | Date in YYYY-MM-DD format |
| `businessUnitId` | number | No | Filter by business unit |

**Approval:** Read
**Response:**
```json
{
  "date": "2026-03-08",
  "total": 12,
  "byStatus": { "Scheduled": 5, "Working": 3, "Completed": 4 },
  "byPriority": { "Normal": 8, "High": 3, "Urgent": 1 },
  "urgentJobs": [{ "jobId": 1234, "priority": "Urgent", ... }]
}
```

**Implementation note:** Fetches up to 100 jobs for the date, aggregates client-side by status and priority. Urgent/High priority jobs returned individually.

---

## Dispatch Tools (6)

### `servicefusion_get_capacity`
Get dispatch capacity/availability for a date range.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `startDate` | string | Yes | Start date |
| `endDate` | string | Yes | End date |
| `businessUnitId` | number | No | Filter by business unit |

**Approval:** Read
**Response:** Capacity/availability slots

---

### `servicefusion_list_technicians`
List all technicians.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `active` | boolean | No | Filter by active status |
| `businessUnitId` | number | No | Filter by business unit |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** `PaginatedResponse<SFTechnician>`

---

### `servicefusion_get_technician`
Get technician details.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `technicianId` | number | Yes | Technician ID |

**Approval:** Read
**Response:** `SFTechnician` object

---

### `servicefusion_list_technician_shifts`
List technician shifts (availability, on-call, time off).

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `technicianId` | number | No | Filter by technician |
| `startsOnOrAfter` | string | No | ISO datetime |
| `startsOnOrBefore` | string | No | ISO datetime |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated shifts with `type` field (`OnCall`, `Available`, `TimeOff`)

---

### `servicefusion_get_on_call_technician`
Find the currently on-call technician.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `businessUnitId` | number | No | Filter by business unit |
| `dateTime` | string | No | Check for specific time (ISO format). Defaults to now. |

**Approval:** Read
**Response:**
```json
{
  "onCall": true,
  "technician": { "id": 42, "name": "John Doe", ... },
  "shift": { "technicianId": 42, "type": "OnCall", "start": "...", "end": "..." }
}
```
or `{ "onCall": false, "message": "No technician on-call" }`

**Implementation note:** Fetches shifts for the target date, filters for `type === "OnCall"` where current time falls within start/end range, then fetches full technician details.

---

### `servicefusion_list_zones`
List dispatch zones.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated zones

---

## Pricebook Tools (7)

### `servicefusion_list_services`
List pricebook services.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `active` | boolean | No | Filter by active status |
| `categoryId` | number | No | Filter by category |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated services

---

### `servicefusion_list_materials`
List pricebook materials.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `active` | boolean | No | Filter by active status |
| `vendor` | string | No | Filter by vendor name |
| `categoryId` | number | No | Filter by category |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** `PaginatedResponse<SFPricebookMaterial>`

---

### `servicefusion_create_material`
Create a new pricebook material.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | string | Yes | — | Material name |
| `code` | string | No | — | Part/SKU code |
| `price` | number | Yes | — | Sell price |
| `cost` | number | Yes | — | Cost/purchase price |
| `vendor` | string | No | — | Vendor name |
| `active` | boolean | No | true | Active in pricebook |

**Approval:** Write (confirmation required)
**Response:** Created material object

---

### `servicefusion_update_material`
Update a pricebook material.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `materialId` | number | Yes | Material ID |
| `name` | string | No | Updated name |
| `price` | number | No | Updated sell price |
| `cost` | number | No | Updated cost |
| `active` | boolean | No | Active status |

**Approval:** Write (confirmation required)
**Response:** Updated material object

---

### `servicefusion_list_equipment`
List pricebook equipment.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `active` | boolean | No | Filter by active status |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated equipment

---

### `servicefusion_list_categories`
List pricebook categories.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated categories

---

### `servicefusion_compare_prices`
Compare vendor prices against current pricebook (for Rexel sync analysis).

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `items` | array | Yes | Array of `{ code: string, newPrice: number, newCost?: number }` |

**Approval:** Read
**Response:**
```json
{
  "compared": 15,
  "increases": 3,
  "decreases": 1,
  "notFound": 2,
  "details": [
    {
      "code": "14-2NM",
      "found": true,
      "currentPrice": 45.99,
      "newPrice": 48.50,
      "change": 2.51,
      "changePercent": 5.46
    },
    {
      "code": "NEW-ITEM",
      "found": false,
      "newPrice": 12.99
    }
  ]
}
```

**Implementation note:** Fetches all materials (up to 1000), indexes by code, then compares each input item. Reports increases, decreases, and not-found items with change amounts and percentages.

---

## Accounting Tools (6)

### `servicefusion_list_invoices`
List invoices.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jobId` | number | No | Filter by job |
| `customerId` | number | No | Filter by customer |
| `status` | `"Pending"` \| `"Posted"` \| `"Exported"` \| `"Void"` | No | Filter by status |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** `PaginatedResponse<SFInvoice>`

---

### `servicefusion_get_invoice`
Get invoice details.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `invoiceId` | number | Yes | Invoice ID |

**Approval:** Read
**Response:** `SFInvoice` object

---

### `servicefusion_list_payments`
List payments.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `customerId` | number | No | Filter by customer |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated payments

---

### `servicefusion_list_estimates`
List estimates/quotes.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jobId` | number | No | Filter by job |
| `status` | `"Open"` \| `"Sold"` \| `"Dismissed"` | No | Filter by status |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** Paginated estimates

---

### `servicefusion_create_estimate`
Create a new estimate.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jobId` | number | Yes | Job to attach estimate to |
| `name` | string | Yes | Estimate name/title |
| `items` | array | Yes | `[{ description: string, quantity: number, unitPrice: number }]` |

**Approval:** Write (confirmation required)
**Response:** Created estimate object

**API path:** `POST /sales/v2/tenant/{tenantId}/jobs/{jobId}/estimates`

---

### `servicefusion_sell_estimate`
Mark an estimate as sold.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `estimateId` | number | Yes | Estimate ID |
| `soldById` | number | Yes | Technician ID who sold it |

**Approval:** Write (confirmation required)
**Response:** Updated estimate object

---

## Telecom Tools (4)

### `servicefusion_list_calls`
List phone calls.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `direction` | `"Inbound"` \| `"Outbound"` | No | Filter by direction |
| `status` | `"Ringing"` \| `"InProgress"` \| `"Completed"` \| `"Missed"` \| `"Voicemail"` | No | Filter by status |
| `createdOnOrAfter` | string | No | ISO date |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read
**Response:** `PaginatedResponse<SFCall>`

---

### `servicefusion_get_call`
Get call details.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `callId` | number | Yes | Call ID |

**Approval:** Read
**Response:** `SFCall` object

---

### `servicefusion_get_missed_calls`
Get recent missed calls for follow-up.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `since` | string | No | 24 hours ago | ISO date — how far back to check |
| `limit` | number | No | 50 | Max results |

**Approval:** Read
**Response:**
```json
{
  "count": 3,
  "since": "2026-03-07T00:00:00.000Z",
  "calls": [{ "callId": 1, "from": "555-0123", ... }]
}
```

---

### `servicefusion_get_calls_with_recordings`
Get completed calls that have recordings (for transcription).

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `since` | string | No | 24 hours ago | ISO date |
| `limit` | number | No | 20 | Max results |

**Approval:** Read
**Response:** `{ count, calls }` — only calls where `recordingUrl` is present

---

## Memberships Tools (3)

### `servicefusion_list_membership_types`
List available membership types/templates.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read

---

### `servicefusion_list_customer_memberships`
List customer memberships.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `customerId` | number | No | Filter by customer |
| `status` | string | No | Filter by status |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read

---

### `servicefusion_list_recurring_services`
List recurring services.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read

---

## Marketing Tools (3)

### `servicefusion_list_campaigns`
List marketing campaigns.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `active` | boolean | No | Filter by active status |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read

---

### `servicefusion_list_campaign_categories`
List campaign categories.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read

---

### `servicefusion_list_campaign_costs`
List campaign costs.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `campaignId` | number | No | Filter by campaign |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read

---

## Settings Tools (3)

### `servicefusion_list_employees`
List all employees.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `active` | boolean | No | Filter by active status |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read

---

### `servicefusion_list_business_units`
List business units.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `active` | boolean | No | Filter by active status |
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read

---

### `servicefusion_list_tag_types`
List tag types.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | number | No | Page number |
| `pageSize` | number | No | Results per page |

**Approval:** Read

---

## Tool Aliases

Every `servicefusion_*` tool has an `sf_*` alias. For example:
- `servicefusion_list_customers` → `sf_list_customers`
- `servicefusion_create_job` → `sf_create_job`

Both names work identically. Use `sf_*` for brevity.

## API Endpoints

| Category | Base Path |
|----------|-----------|
| CRM | `/crm/v2/tenant/{tenantId}` |
| Jobs | `/jpm/v2/tenant/{tenantId}` |
| Dispatch | `/dispatch/v2/tenant/{tenantId}` |
| Pricebook | `/pricebook/v2/tenant/{tenantId}` |
| Accounting | `/accounting/v2/tenant/{tenantId}` |
| Sales (Estimates) | `/sales/v2/tenant/{tenantId}` |
| Telecom | `/telecom/v2/tenant/{tenantId}` |
| Memberships | `/memberships/v2/tenant/{tenantId}` |
| Marketing | `/marketing/v2/tenant/{tenantId}` |
| Settings | `/settings/v2/tenant/{tenantId}` |

---

*Generated from MCP server source | 2026-03-08*
