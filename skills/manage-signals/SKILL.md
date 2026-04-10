---
name: manage-signals
description: >
  View, pause, resume, and adjust your active Saber signal subscriptions.
---

# Manage Signals

Use this skill to get an overview of your active signal subscriptions and manage them — pause ones that aren't useful, resume paused ones, adjust frequency, or clean up old subscriptions.

## Saber CLI check

Before doing anything else, check if the Saber CLI is installed by running `saber --help`.

**If not installed:** inform the user that managing signals requires the Saber CLI (available at saber.app).

## Step 1 — Fetch all subscriptions

```bash
saber subscription list
```

Present the results in a clear table:

| ID | Name | List | Frequency | Status | Last run |
|----|------|------|-----------|--------|----------|
| abc | Hiring signal | Tier 1 Accounts | weekly | active | 2026-04-08 |
| ... | | | | | |

Group by status: **active**, **paused**, **completed** (run-once subscriptions that finished).

## Step 2 — Ask what the user wants to do

Present the options:
1. **View results** for a specific subscription
2. **Pause** one or more subscriptions
3. **Resume** a paused subscription
4. **Trigger** a subscription to run now (outside its normal schedule)
5. **Stop** a subscription permanently
6. **Review all results** — surface a summary across all active subscriptions

## Step 3 — Execute the chosen action

### View results
```bash
saber subscription get <subscriptionId>
```
Present results clearly. If the signal is a boolean, show what percentage of companies returned positive.

### Pause
```bash
saber subscription stop <subscriptionId>
```
Note: `stop` pauses the schedule but keeps the subscription and its results intact.

### Resume
```bash
saber subscription start <subscriptionId>
```

### Trigger now
```bash
saber subscription trigger <subscriptionId>
```
Useful for running a subscription ahead of schedule (e.g. before a sales review).

### Stop permanently
Confirm with the user before stopping:
> "Stopping permanently will cancel the subscription. Results already collected are preserved. Are you sure?"
```bash
saber subscription stop <subscriptionId>
```

## Step 4 — Review mode (optional)

If the user chooses "Review all results", fetch and summarise results across all active subscriptions:

```bash
saber subscription get <subscriptionId>   # for each active subscription
```

For each subscription, report:
- How many companies were checked
- What percentage returned positive
- When it last ran and when it runs next

Highlight subscriptions where:
- A high percentage of companies are positive (strong signal, prioritise outreach)
- No companies are positive (signal may need rewording or the list may need refreshing)
- The last run was more than 30 days ago (may be stale)

Suggest next steps based on the review, such as using `score-accounts` to rank accounts by combined signal strength.
