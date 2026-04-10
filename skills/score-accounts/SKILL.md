---
name: score-accounts
description: >
  Score and rank a list of accounts by signal strength to surface the highest-priority
  targets for outreach. Works with Saber signal results, Apollo data, HubSpot properties,
  or any pasted signal data.
---

# Score Accounts

Use this skill to rank accounts by the strength of their signals so you can focus outreach
on the highest-intent targets first.

Works with signal data from any source — Saber subscriptions, Apollo exports, HubSpot
properties, or manually researched signals. The scoring algorithm is the same regardless
of where the data comes from.

## Step 1 — Get the signal data

Ask the user how their signal data is available:

---

### Path A — Saber CLI

Run `saber --help` to confirm the CLI is installed.

Find the account list and active subscriptions:
```bash
saber list company list
saber subscription list
```

Fetch results for each relevant subscription:
```bash
saber subscription get <subscriptionId>
```

If no subscriptions have run yet, offer to activate signals first using `create-company-signals`, then return here.

---

### Path B — Any other source

Ask the user to provide signal results. Accepted formats:
- Paste a table or CSV with columns: Company, Domain, [Signal 1], [Signal 2], ...
- Describe what they know for each account (e.g. "Acme raised a round, Beta is hiring SDRs")
- Share a HubSpot export or Apollo enrichment output

Confirm which signals are present and what a positive result looks like for each.

---

## Step 2 — Apply the scoring model

### If signal metadata is available (from `generate-signals`)

Use the weighted algorithm:

```
1. For each signal, determine if the answer is "positive" per interpretation rules
2. earnedWeight = sum of weights for positive-answer signals
3. totalWeight  = sum of all signal weights
4. rawScore     = (earnedWeight / totalWeight) × 100

5. Apply penalties:
   — Any disqualifier fires wrong  → score = 0 immediately
   — Zero buying_signal hits       → score = 0 (no pain evidence)
   — Zero urgency hits             → score − 15 (right fit, wrong time)

6. Final score: 0–100
```

**Thresholds:**
- **70+** — High fit, prioritise outreach
- **50–69** — Moderate fit, worth pursuing
- **30–49** — Low fit, monitor for triggers
- **< 30** — Poor fit, deprioritise

### If no signal metadata is available

Ask the user if any signals are more important than others. Then apply a simple model:
- **Base:** +1 per positive signal
- **Priority signals:** 2× weight for signals the user flags as most important
- **Recency bonus:** +0.5 if the signal is from the last 7 days
- Normalise to 0–10 relative to the highest scorer

## Step 3 — Present ranked results

```
## Account Scores — [List Name]

### High priority (70+ or top tier)
| Rank | Company | Domain | Score | Top signals |
|------|---------|--------|-------|-------------|
| 1 | Acme Corp | acme.com | 84 | New VP Sales hired, hiring SDRs |
| 2 | Beta Inc | beta.io | 76 | Series B 3 months ago, HubSpot migration |

### Watch list (moderate fit)
| Rank | Company | Domain | Score | Top signal |
|------|---------|--------|-------|------------|
| ...  | | | | |

### Deprioritise (low fit)
| Rank | Company | Domain | Score | Notes |
|------|---------|--------|-------|-------|
| ...  | | | | |
```

## Step 4 — Suggest next steps

- **High-priority accounts:** use `write-outreach` to draft personalised messages referencing the top signal
- **Watch list:** re-run signals in 2–4 weeks to watch for urgency triggers
- **Low-scoring accounts:** pause or remove from active list; revisit if a trigger fires
- Use `deal-coaching` on any high-priority account that's already in the pipeline
