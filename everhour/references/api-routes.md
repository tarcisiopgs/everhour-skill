# Everhour API Routes Reference

Base URL: `https://api.everhour.com`
Auth headers (required on every request):
```
X-Api-Key: $EVERHOUR_API_KEY
X-Accept-Version: 1.2
Content-Type: application/json   # only for POST/PUT/PATCH
```

---

## Table of Contents

1. [Clients](#clients)
2. [Invoices](#invoices)
3. [Expenses](#expenses)
4. [Schedule (Assignments)](#schedule-assignments)
5. [Time Off](#time-off)
6. [Projects](#projects)
7. [Tasks](#tasks)
8. [Custom Fields](#custom-fields)
9. [Time Records](#time-records)
10. [Timers](#timers)
11. [Timecards](#timecards)
12. [Timesheets](#timesheets)
13. [Reports](#reports)
14. [Users](#users)
15. [Webhooks](#webhooks)

---

## Clients

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/clients` | Get All Clients |
| GET | `/clients/{id}` | Get Client |
| POST | `/clients` | Create Client |
| PUT | `/clients/{id}` | Update Client |
| PUT | `/clients/{id}/budget` | Update Client Budget |
| DELETE | `/clients/{id}/budget` | Delete Client Budget |

**Create/Update Client body:**
```json
{
  "name": "Client Name",
  "contactName": "Jane Doe",
  "email": "jane@example.com",
  "phone": "+1234567890",
  "notes": "optional notes"
}
```

**Update Client Budget body:**
```json
{
  "budget": 100000,
  "budgetType": "money",
  "budgetProgress": "reported"
}
```

---

## Invoices

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/invoices` | Get All Invoices |
| GET | `/invoices/{id}` | Get Invoice |
| POST | `/invoices` | Create Invoice |
| PUT | `/invoices/{id}` | Update Invoice |
| POST | `/invoices/{id}/refresh` | Refresh Invoice Line Items |
| PUT | `/invoices/{id}/status` | Mark Invoice as Draft/Sent/Paid |
| POST | `/invoices/{id}/export` | Export Invoice to Xero/QB/FB |
| DELETE | `/invoices/{id}` | Delete Invoice |

**Mark Invoice Status body:**
```json
{ "status": "draft" }
// status options: "draft", "sent", "paid"
```

**Export Invoice body:**
```json
{ "provider": "xero" }
// provider options: "xero", "qb", "fb"
```

---

## Expenses

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/expenses` | Get All Expenses |
| POST | `/expenses` | Create Expense |
| PUT | `/expenses/{id}` | Update Expense |
| DELETE | `/expenses/{id}` | Delete Expense |
| GET | `/expenses/categories` | Get All Categories |
| POST | `/expenses/categories` | Create Category |
| PUT | `/expenses/categories/{id}` | Update Category |
| DELETE | `/expenses/categories/{id}` | Delete Category |
| POST | `/expenses/attachments` | Create Attachment |
| POST | `/expenses/{id}/attachments` | Add Attachment To Expense |
| GET | `/expenses/attachments/{id}` | Download Attachment |
| DELETE | `/expenses/attachments/{id}` | Delete Attachment |

**Create Expense body:**
```json
{
  "date": "2025-08-25",
  "amount": 5000,
  "note": "Team lunch",
  "categoryId": 123,
  "projectId": "ev:project_id"
}
```

---

## Schedule (Assignments)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/assignments` | Get All Assignments |
| POST | `/assignments` | Create Assignment |
| PUT | `/assignments/{id}` | Update Assignment |
| DELETE | `/assignments/{id}` | Delete Assignment |

**Create Assignment body:**
```json
{
  "userId": 123,
  "projectId": "ev:project_id",
  "taskId": "ev:task_id",
  "startDate": "2025-08-25",
  "endDate": "2025-08-31",
  "time": 28800
}
```

---

## Time Off

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/time-off` | Create Time Off |
| GET | `/time-off/types` | Get Time Off Types |
| POST | `/time-off/types` | Create Time Off Type |
| PUT | `/time-off/types/{id}` | Update Time Off Type |
| DELETE | `/time-off/types/{id}` | Delete Time Off Type |
| GET | `/time-off/allocations` | Get All Allocations |
| POST | `/time-off/allocations` | Create Allocation |
| PUT | `/time-off/allocations/{id}` | Update Allocation |
| DELETE | `/time-off/allocations/{id}` | Delete Allocation |

**Create Time Off body:**
```json
{
  "userId": 123,
  "typeId": 1,
  "startDate": "2025-08-25",
  "endDate": "2025-08-29"
}
```

**Create Time Off Type body:**
```json
{
  "name": "Vacation",
  "color": "#FF5733"
}
```

---

## Projects

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/projects` | Get All Projects |
| GET | `/projects/{id}` | Get Project |
| POST | `/projects` | Create Project |
| PUT | `/projects/{id}` | Update Project |
| PUT | `/projects/{id}/archive` | Archive/Unarchive Project |
| PUT | `/projects/{id}/billing` | Update Project Billing/Budget |
| DELETE | `/projects/{id}` | Delete Project |
| GET | `/projects/{id}/sections` | Get Project Sections |
| GET | `/sections/{id}` | Get Section |
| POST | `/projects/{id}/sections` | Create Section |
| PUT | `/sections/{id}` | Update Section |
| DELETE | `/sections/{id}` | Delete Section |
| POST | `/projects/{id}/sync` | Sync Integration Project |

**Get All Projects query params:**
- `limit` — max results
- `page` — page number
- `status` — `active`, `archived`

**Create Project body:**
```json
{
  "name": "My Project",
  "clientId": 123,
  "color": "#FF5733"
}
```

**Archive/Unarchive body:**
```json
{ "archived": true }
```

**Update Billing/Budget body:**
```json
{
  "budget": 100000,
  "budgetType": "money",
  "rate": 10000,
  "rateType": "project"
}
```

---

## Tasks

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/projects/{id}/tasks` | Get Project Tasks |
| GET | `/tasks/{id}` | Get Task |
| GET | `/tasks/{id}/billing` | Get Task Billing |
| GET | `/tasks?query={q}` | Search Tasks |
| GET | `/projects/{id}/tasks/search?query={q}` | Search Project Tasks |
| POST | `/projects/{id}/tasks` | Create Task |
| PUT | `/tasks/{id}` | Update Task |
| DELETE | `/tasks/{id}` | Delete Task |
| PUT | `/tasks/{id}/estimate` | Update Task Estimate |
| DELETE | `/tasks/{id}/estimate` | Delete Task Estimate |

**Create Task body:**
```json
{
  "name": "Task Name",
  "sectionId": 456,
  "dueDate": "2025-09-01"
}
```

**Update Task Estimate body:**
```json
{ "time": 7200 }
```

---

## Custom Fields

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/projects/{id}/fields` | Get Project Fields Configuration |
| POST | `/projects/{id}/fields` | Create Field in Project |
| PUT | `/fields/{id}` | Update Field |
| DELETE | `/fields/{id}` | Delete Field |
| PUT | `/projects/{id}/fields/reorder` | Reorder Fields |
| PUT | `/tasks/{id}/fields` | Update Task Custom Fields |

**Create Field body:**
```json
{
  "name": "Priority",
  "type": "select",
  "options": ["Low", "Medium", "High"]
}
```

**Update Task Custom Fields body:**
```json
{
  "fields": {
    "field_id_1": "value",
    "field_id_2": 42
  }
}
```

---

## Time Records

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/time/records` | Get All Time Records |
| GET | `/users/{id}/time` | Get User Time Records |
| GET | `/tasks/{id}/time` | Get Task Time Records |
| GET | `/projects/{id}/time` | Get Project Time Records |
| GET | `/time/billing` | Get Time Billing |
| POST | `/time/records` | Add Time |
| PUT | `/time/records/{id}` | Update Time Record |
| DELETE | `/time/records/{id}` | Delete Time Record |

**Get All Time Records query params:**
- `from` — start date (YYYY-MM-DD)
- `to` — end date (YYYY-MM-DD)
- `limit` — max results
- `page`
- `userId`
- `projectId`

**Add Time body:**
```json
{
  "task": "ev:TASK_ID",
  "time": 7200,
  "date": "2025-08-25",
  "comment": "optional note"
}
```

**Update Time Record body:**
```json
{
  "time": 3600,
  "comment": "updated note"
}
```

---

## Timers

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/timers` | Start Timer |
| GET | `/timers/current` | Get Running Timer |
| GET | `/timers` | Get All Team Timers |
| DELETE | `/timers/current` | Stop Timer |

**Start Timer body:**
```json
{
  "task": "ev:TASK_ID",
  "comment": "optional note"
}
```

> Note: Only one timer can run at a time per user. Starting a new timer stops the current one.

---

## Timecards

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/timecards/{id}` | Get Timecard |
| GET | `/users/{id}/timecards` | Get User Timecards |
| GET | `/timecards` | Get All Timecards |
| POST | `/timecards/clock-in` | Clock In |
| POST | `/timecards/clock-out` | Clock Out |
| PUT | `/timecards/{id}` | Update Timecard |
| DELETE | `/timecards/{id}` | Delete Timecard |

**Clock In body:**
```json
{}
// or with specific time:
{ "time": "09:00" }
```

**Clock Out body:**
```json
{}
// or with specific time:
{ "time": "18:00" }
```

---

## Timesheets

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/users/{id}/timesheets` | Get User Timesheets |
| GET | `/timesheets` | Get Team Timesheets |
| POST | `/timesheets/{id}/approve` | Approve Week / Request for Approval |
| DELETE | `/timesheets/{id}/approve` | Discard Your Approval Request |
| PUT | `/timesheets/{id}/approve` | Approve or Reject Approval Request |

**Week ID**: `YY` + ISO week number → week 35 of 2025 = `2535`

**Timesheet ID**: `userId` + `weekId` → user 14856, week 2535 = `148562535`

**Approve or Reject body:**
```json
{ "action": "approve" }
// action options: "approve", "reject"
```

---

## Reports

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/reports/projects` | Projects Report |
| GET | `/reports/clients` | Clients Report |
| GET | `/reports/users` | Users Report |

**Common query params for reports:**
- `from` — start date (YYYY-MM-DD)
- `to` — end date (YYYY-MM-DD)
- `projectIds[]` — filter by project IDs
- `userIds[]` — filter by user IDs
- `clientIds[]` — filter by client IDs

> All time columns in reports are in **seconds**. All amount columns are in **cents**.

---

## Users

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/users/me` | Get Current User |
| GET | `/users` | Get All Users |

---

## Webhooks

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/webhooks/{id}` | Get Webhook |
| POST | `/webhooks` | Create Webhook |
| PUT | `/webhooks/{id}` | Update Webhook |
| DELETE | `/webhooks/{id}` | Delete Webhook |

**Create Webhook body:**
```json
{
  "targetUrl": "https://your-server.com/webhook",
  "events": [
    "api:timer:started",
    "api:timer:stopped",
    "api:time:updated"
  ]
}
```

**Supported webhook events:**
```
api:project:created    api:project:updated    api:project:removed
api:task:created       api:task:updated       api:task:removed
api:timer:started      api:timer:stopped
api:time:updated
api:section:created    api:section:updated    api:section:removed
api:client:created     api:client:updated
api:estimate:updated
```

**Webhook verification:** When you create or update a webhook, Everhour sends a POST to your target URL with an `X-Hook-Secret` header and empty body. Your endpoint must respond with the same `X-Hook-Secret` header to complete verification.
