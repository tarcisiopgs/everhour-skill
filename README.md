# everhour-skill

An AI agent skill for interacting with the [Everhour](https://everhour.com) time tracking API via natural language.

## What it does

Once installed, this skill lets you control Everhour directly from your AI assistant:

- Track time: start/stop timers, add time to tasks, manage timecards
- Manage projects, tasks, clients, expenses, and invoices
- Generate reports (projects, clients, users)
- Manage schedules, time off, timesheets, and webhooks

## Setup

Set your Everhour API key as an environment variable:

```bash
export EVERHOUR_API_KEY=your-api-key-here
```

You can find your API key in your [Everhour profile](https://app.everhour.com/#/account/profile) at the bottom of the page.

## Usage examples

```
start a timer on task ev:12345
stop my current timer
add 2 hours to task ev:12345 for today
list all active projects
show me the projects report for August 2025
create a client named Acme Corp
clock me in
get all team timers
```

## Installation

Install using any SKILL.md-compatible AI tool by pointing to this repository.

## API Coverage

Covers the full Everhour REST API v1.2:

- Clients, Invoices, Expenses
- Projects, Tasks, Custom Fields, Sections
- Time Records, Timers, Timecards, Timesheets
- Schedule, Time Off
- Reports (Projects, Clients, Users)
- Users, Webhooks
