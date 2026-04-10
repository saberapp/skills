---
name: score-accounts
description: >
  Score and rank a Saber account list by signal strength to surface the highest-priority
  targets for outreach.
---

# Score Accounts

Use this skill to rank the companies in a Saber account list by the strength of their
signal results, so you can focus outreach on the highest-intent accounts.

## Saber CLI check

Run `saber --help` to check if the Saber CLI is installed.

**If not installed:** inform the user that scoring accounts requires the Saber CLI
(available at saber.app). Offer to work manually if the user can paste signal results directly.

## Step 1 — Identify the list and signals

Ask the user which list they want to score, or help them find it:
```bash
saber list company list
```

Then find the active signal subscriptions for that list:
```bash
saber subscription list
```

If no subscriptions have been run yet, offer to run signals first using `create-company-signals`, then return here.

## Step 2 — Fetch signal results

For each relevant subscription, retrieve results:
```bash
saber subscription get <subscriptionId>
```

Collect results for all companies and all signal questions into a working dataset. Note:
- Which companies had which signals fire positively
- How recently the signals ran (fresher = more reliable)
- Each signal's category (`icp_fit`, `urgency`, `buying_signal`) and weight (1–3) if available
  from `generate-signals` output in conversation context

## Step 3 — Score each company

### If signal metadata is available (from `generate-signals`)

Use the weighted scoring algorithm:

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

### If no signal metadata is available (ad-hoc signals)

Fall back to a simple model:
- **Base score:** +1 per positive signal
- **Weighting:** ask the user if any signals are more important; weight those 2×
- **Recency bonus:** +0.5 if the signal ran within the last 7 days
- Normalise to 0–10 relative to the highest scorer in the list

## Step 4 — Present ranked results

```
## Account Scores — [List Name]

### High fit (70+)
| Rank | Company | Domain | Score | Signals fired | Top signal |
|------|---------|--------|-------|---------------|------------|
| 1    | Acme Corp | acme.com | 84 | 4/5 icp_fit, 2/3 urgency | Recently funded |

### Moderate fit (50–69)
| Rank | Company | Domain | Score | Signals fired | Top signal |
|------|---------|--------|-------|---------------|------------|
| ...  | | | | | |

### Low fit / monitor (< 50)
| Rank | Company | Domain | Score | Notes |
|------|---------|--------|-------|-------|
| ...  | | | | |
```

## Step 5 — Suggest next steps

- **High-fit accounts:** use `write-outreach` to draft personalised messages referencing the top signal
- **Moderate accounts:** re-run signals in 2–4 weeks to watch for urgency triggers
- **Low-scoring accounts:** pause or remove from the active list; revisit if a trigger fires
