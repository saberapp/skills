---
name: pipeline-review
description: >
  Audit pipeline health from CRM data — identify at-risk deals, surface stalled
  opportunities, flag forecast risk, and recommend where to focus.
---

# Pipeline Review

Use this skill to get a structured health check on your pipeline. Bring your deal data
and get back: a coverage assessment, at-risk deal flags, stall analysis, and a recommended
focus list for the week.

Works with HubSpot MCP (if connected), pasted deal data, or a CSV export from your CRM.
No Saber CLI required.

## Input

Ask the user how they want to provide pipeline data:

**Option A — HubSpot MCP (if available)**
Check for HubSpot tools in the available tool list. If connected, pull deals directly:
```
Fetch all open deals from HubSpot, including: deal name, company, stage, amount,
close date, last activity date, owner, and any custom signal properties.
```

**Option B — Paste or describe**
Ask the user to paste a pipeline export or describe their open deals. Minimum useful fields:
- Company name
- Deal stage
- Deal value / ACV
- Expected close date
- Last activity date
- Owner

**Option C — CSV**
If the user uploads a CSV, parse it and proceed with whatever fields are present.

## Step 1 — Pipeline overview

Calculate and present:

```
PIPELINE OVERVIEW

Total open pipeline:  $[X]
Deal count:           [N] deals across [N] reps
Average deal size:    $[X]
Weighted pipeline:    $[X] (stage-weighted by typical conversion rates)

BY STAGE
  [Stage name]:  [N] deals, $[X] value, avg [N] days in stage
  [...]

COVERAGE
  Quota this period:  $[X]
  Pipeline:           $[X]
  Coverage ratio:     [X]x
  [Flag if below 3x]
```

## Step 2 — At-risk deals

Flag deals showing risk signals:

**Stalled** — in the same stage for longer than typical
**No recent activity** — no logged activity in 14+ days
**Slipping close date** — close date has moved more than once
**Single-threaded** — only one contact on the deal
**Missing next step** — no scheduled next activity
**Late-stage with no economic buyer** — in proposal/negotiation but no exec engaged

```
AT-RISK DEALS

🔴 [Company] — $[X] — [Stage]
   Risk: [Stalled 28 days / No activity since [date] / Close date moved 3x]
   Owner: [Name]
   Recommended action: [Specific suggested next step]

🟡 [Company] — $[X] — [Stage]
   Risk: [Watch area]
   [...]
```

## Step 3 — Forecast analysis

Assess commit and best-case forecast:

```
FORECAST

Commit (high confidence):     $[X]  — [N] deals
Best case (likely to close):  $[X]  — [N] deals
Pipeline (needs work):        $[X]  — [N] deals

FORECAST RISK
  [Deal name]: $[X] — [reason this might not close as forecast]
  [...]

GAP TO QUOTA
  [If commit covers quota: "Commit covers quota — focus on best-case upside"]
  [If gap exists: "Gap of $[X] — need to close [N] deals from best-case or pull something forward"]
```

## Step 4 — Where to focus

Recommend the top 5 deals to prioritise this week, with reasoning:

```
FOCUS LIST — This Week

1. [Company] — $[X] — [Stage]
   Why: [Highest value deal with a clear path to close / decision expected this week / at risk of stalling]
   Next step: [Specific action]

2. [...]
```

## Step 5 — Pipeline health score

Rate overall pipeline health:

- **Coverage** — is there enough pipeline to hit quota?
- **Velocity** — are deals moving through stages at a healthy pace?
- **Distribution** — is pipeline too concentrated in one stage or one rep?
- **Quality** — are deals well-qualified or are there a lot of early-stage unknowns?

```
PIPELINE HEALTH

Coverage:     [Green / Yellow / Red] — [X]x coverage
Velocity:     [Green / Yellow / Red] — avg [N] days per stage
Distribution: [Green / Yellow / Red] — [observation]
Quality:      [Green / Yellow / Red] — [observation]

OVERALL: [Healthy / Needs attention / At risk]
```

## Step 6 — Suggest next steps

- Use `deal-coaching` on the top 2–3 at-risk deals for deeper analysis
- Use `write-outreach` to re-engage stalled deals with a signal-informed message
- Use `find-expansion-accounts` to identify upsell opportunities in the existing customer base
- Schedule a follow-up pipeline review in 2 weeks to track movement
