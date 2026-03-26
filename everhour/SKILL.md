---
name: everhour
description: >
  Interact with the Everhour time tracking API using natural language. Use this skill whenever
  the user wants to track time, manage projects, tasks, clients, timers, timecards, timesheets,
  expenses, invoices, reports, or webhooks in Everhour — even if they don't say "Everhour"
  explicitly. Trigger on requests like: "start a timer", "log 2 hours to this task", "list my
  projects", "show my running timer", "add time to ev:12345", "clock in", "get projects report",
  "create a client", or "what timers are running for the team". Requires EVERHOUR_API_KEY env var.
---

# Everhour API Skill

Everhour is a time tracking tool. This skill lets you call the Everhour REST API via `curl`.

## Authentication

Every request needs these headers:

```bash
-H "X-Api-Key: $EVERHOUR_API_KEY" \
-H "X-Accept-Version: 1.2" \
-H "Content-Type: application/json"
```

Base URL: `https://api.everhour.com`

Verify the env var exists before proceeding:

```bash
[ -z "$EVERHOUR_API_KEY" ] && echo "Error: EVERHOUR_API_KEY is not set." && exit 1
```

## Key Data Conventions

- **Time**: always in **seconds** (3600 = 1h, 5400 = 1h30m)
- **Money**: always in **cents** (500 = $5.00)
- **Rate limit**: ~20 req/10s. On HTTP 429, wait `Retry-After` seconds and retry.
- **API version**: 1.2 (use `X-Accept-Version: 1.2` header)

## Workflow

1. Parse the user's intent and map it to an endpoint (see `references/api-routes.md`)
2. Build the `curl` command with auth headers
3. Execute and capture the JSON response
4. Format the output as readable markdown — don't dump raw JSON at the user
5. Handle errors gracefully (missing key, 401, 404, 429, etc.)

## Common Operations & Examples

### Check who you are
```bash
curl -s "https://api.everhour.com/users/me" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2"
```

### List projects
```bash
curl -s "https://api.everhour.com/projects" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2"
```

### Start a timer on a task
```bash
curl -s -X POST "https://api.everhour.com/timers" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2" \
  -H "Content-Type: application/json" \
  -d '{"task": "ev:TASK_ID"}'
```

### Stop the running timer
```bash
curl -s -X DELETE "https://api.everhour.com/timers/current" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2"
```

### Add time to a task
```bash
# time in seconds: 7200 = 2h
curl -s -X POST "https://api.everhour.com/time/records" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2" \
  -H "Content-Type: application/json" \
  -d '{"task": "ev:TASK_ID", "time": 7200, "date": "2025-08-25"}'
```

### Get all time records (with filters)
```bash
# Supports: from, to (YYYY-MM-DD), limit, page, userId, projectId
curl -s "https://api.everhour.com/time/records?from=2025-08-01&to=2025-08-31" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2"
```

### Search tasks
```bash
curl -s "https://api.everhour.com/tasks?query=SEARCH_TERM" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2"
```

### Get project report
```bash
# from/to are YYYY-MM-DD
curl -s "https://api.everhour.com/reports/projects?from=2025-08-01&to=2025-08-31" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2"
```

### Clock in (timecard)
```bash
curl -s -X POST "https://api.everhour.com/timecards/clock-in" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2" \
  -H "Content-Type: application/json" \
  -d '{}'
```

### Clock out
```bash
curl -s -X POST "https://api.everhour.com/timecards/clock-out" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2" \
  -H "Content-Type: application/json" \
  -d '{}'
```

## Timesheets — Special IDs

Timesheets use composite IDs:

- **Week ID** = last 2 digits of year + ISO week number → e.g., week 35 of 2025 = `2535`
- **Timesheet ID** = userId + weekId → e.g., user 14856, week 2535 = `148562535`

```bash
# Get user's timesheets
curl -s "https://api.everhour.com/users/USER_ID/timesheets" \
  -H "X-Api-Key: $EVERHOUR_API_KEY" \
  -H "X-Accept-Version: 1.2"
```

## Error Handling

| Status | Meaning | Action |
|--------|---------|--------|
| 401 | Invalid API key | Tell user to check `EVERHOUR_API_KEY` |
| 403 | Insufficient permissions | Explain permission needed |
| 404 | Resource not found | Check the ID provided |
| 429 | Rate limit exceeded | Read `Retry-After` header and wait |
| 500 | Server error | Retry once; if it persists, report it |

## Output Formatting

Format responses as clear markdown. Examples:

**Timer started:**
> ✓ Timer started on task "Fix login bug" (ev:12345)

**Time added:**
> ✓ Added 2h to "Fix login bug" on 2025-08-25

**Project list:**
> | Name | Status | ID |
> |------|--------|----|
> | Acme Website | active | ev:abc |

Do not dump raw JSON unless the user explicitly asks for it.

## Full API Reference

For the complete list of all endpoints, parameters, and request bodies, see:
→ `references/api-routes.md`

Use this file when you need an endpoint not covered by the examples above.
