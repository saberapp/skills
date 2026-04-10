---
name: qualify-inbound
description: >
  Qualify an inbound lead by running your defined signals against their company domain
  and producing a scored qualification summary.
---

# Qualify Inbound

Use this skill to quickly qualify an inbound lead by running your active signal definitions against their company domain and scoring the result.

## Saber CLI check

Before doing anything else, check if the Saber CLI is installed by running `saber --help`.

**If not installed:** inform the user that qualifying inbound leads requires the Saber CLI (available at saber.app). Offer to continue without it by asking the user to describe the lead and manually assessing fit against their ICP from conversation context.

## Step 1 — Get the lead

Ask for the inbound company's domain (e.g. `acme.com`). If a company name is provided instead, ask for the domain or attempt to infer it.

## Step 2 — Load signal definitions

Check for approved signal definitions in conversation context. If none are available, ask the user:

> "Do you have defined signal questions for your ICP? If not, run `signal-discovery` first to set them up."

If the user has signals defined (even informally, e.g. "we care about companies hiring in sales"), proceed with those.

## Step 3 — Check for existing signal data

Before running new signals (which costs credits), check if signals have already been run for this domain:

```bash
saber subscription list
```

Scan the results for any subscription that might have already processed this company. If results exist, use them and skip to Step 5.

## Step 4 — Run signals

Show the user their credit balance first:
```bash
saber credits
```

Tell them: "Running [N] signals against [domain] will use [N] credits." Ask them to confirm.

Then run each signal question against the domain:
```bash
saber signal --domain <domain> --question "<signal question>" --answer-type boolean
```

For multiple signals, use `--no-wait` and collect results:
```bash
saber signal --domain <domain> --question "<question>" --answer-type boolean --no-wait
# Collect signal IDs, then:
saber signal get <signalId>
```

## Step 5 — Score the lead

Based on the signal results, produce a qualification score:

**Scoring logic:**
- Count how many signals fired positively
- Weight signals by importance if the user has indicated which matter most
- Factor in any disqualifying signals (e.g. "Is this company a competitor?")

**Output tiers:**
- **Strong fit** — majority of signals positive, no disqualifiers
- **Possible fit** — mixed signals, worth a follow-up conversation
- **Weak fit** — few positive signals or a disqualifier fired
- **Not a fit** — disqualifying signal fired or no positive signals

## Step 6 — Present qualification summary

```
## Qualification: [Company] ([domain])

**Score:** Strong fit / Possible fit / Weak fit / Not a fit

**Signal results:**
| Signal | Result | Notes |
|--------|--------|-------|
| [question] | ✅ Yes / ❌ No | [any context from result] |

**Recommendation:** [1–2 sentences explaining the score and suggested next action]
```

## Step 7 — Suggest next steps

- **Strong fit:** suggest `write-outreach` to draft a personalised first touch
- **Possible fit:** suggest `research-account` to get more context before reaching out
- **Weak/Not a fit:** suggest routing to a nurture sequence or disqualifying
