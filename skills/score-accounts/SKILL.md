---
name: score-accounts
description: >
  Score and rank a Saber account list by signal strength to surface the highest-priority
  targets for outreach.
---

# Score Accounts

Use this skill to rank the companies in a Saber account list by the strength of their signal results, so you can focus outreach on the highest-intent accounts.

## Saber CLI check

Before doing anything else, check if the Saber CLI is installed by running `saber --help`.

**If not installed:** inform the user that scoring accounts requires the Saber CLI (available at saber.app). Offer to work manually if the user can paste in signal results directly.

## Prerequisites

- A Saber account list exists with at least some signal results (subscriptions that have run)
- Saber CLI is available

## Step 1 — Identify the list and signals

Ask the user which list they want to score, or help them find it:
```bash
saber list company list
```

Then find the active signal subscriptions for that list:
```bash
saber subscription list
```

If no subscriptions have been run yet, offer to run signals first using the `create-company-signals` skill, then return here.

## Step 2 — Fetch signal results

For each relevant subscription, retrieve results:
```bash
saber subscription get <subscriptionId>
```

Collect the results for all companies and all signal questions into a working dataset. Note:
- Which companies had which signals fire positively
- How recently the signals ran (fresher data = more reliable)
- How many total signals were checked per company

## Step 3 — Score each company

Apply a simple scoring model:

**Base score:** +1 point per positive signal
**Recency bonus:** +0.5 if the signal ran within the last 7 days
**Signal weighting:** ask the user if any signals are more important than others (e.g. "hiring in sales is our strongest signal"). If yes, weight those signals 2×.

Normalise scores to a 0–10 scale relative to the highest scorer in the list.

If the user has ICP criteria in conversation context (from `signal-discovery`), also apply:
- **Disqualifier penalty:** −5 if any disqualifying signal fired

## Step 4 — Present ranked results

```
## Account Scores — [List Name]

| Rank | Company | Domain | Score | Signals Fired | Top Signal |
|------|---------|--------|-------|---------------|------------|
| 1 | Acme Corp | acme.com | 9.2 | 4/5 | Hiring in sales |
| 2 | Beta Inc | beta.io | 7.8 | 3/5 | Recently funded |
| ... | | | | | |
```

Highlight the top 10% as **Priority** accounts — these should be the first batch for outreach.

## Step 5 — Suggest next steps

- **Top accounts:** use `write-outreach` to draft personalised messages
- **Mid-tier accounts:** suggest re-running signals in 2–4 weeks to watch for movement
- **Low-scoring accounts:** suggest pausing or removing from the active list to focus effort
