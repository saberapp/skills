---
name: generate-signals
description: >
  Generate 12–15 structured, weighted research signals from a structured ICP, ready
  to activate in Saber. Each signal includes answer type, weight, and interpretation
  rules for automatic scoring.
---

# Generate Signals

Use this skill to turn a structured ICP into a full signal set — 12–15 research questions
that can be run against any list of prospect companies in Saber.

Run `extract-icp` first if you don't have a structured ICP yet.

## Input

Provide the ICP output from `extract-icp`, including:
- Pain points
- Buying triggers
- Target company profile (size, industry, stage)
- Optional: targeting context (e.g. "focus on NetSuite users")

## The core insight: inverse pain points

> **Pain points describe what customers LACKED before buying.**
> Good prospects ALSO lack these things right now.

When generating signals, look for companies that *currently have the problem* — not ones that already solved it.

| Pain point | What you're actually looking for |
|---|---|
| "No in-house marketing expertise" | Companies WITHOUT a dedicated marketing team |
| "Manual reconciliation errors" | Companies that lack financial automation |
| "Reporting takes days to produce" | Companies using spreadsheets for reporting |
| "Rapid headcount growth overwhelming ops" | Companies with 30%+ headcount growth in 12 months |

## The Signal Library — use it where it fits

Saber ships five prebuilt **Signal Library** signals. They are curated, evidence-cited
research signals with stable answers — run by key, no question design needed:

| Library key | Covers |
|---|---|
| `funding` | Funding history and recent rounds |
| `mna` | M&A activity — as acquirer or as target |
| `tech` | Technology stack, by category (`erp`, `crm`) or by named product |
| `open-jobs` | Open roles and hiring activity |
| `firmographics` | Size, HQ, industry, and other company fundamentals |

**When a signal you're about to write matches a library key, use the library signal instead
of a hand-written question.** It is evidence-cited, stable across runs, and needs no
interpretation tuning. Mark it in the output as `LibraryKey: <key>` in place of `Question:`.

The library is not a preset layer on top of custom signals — it's the shortest path to the
signals most teams need first. Everything it doesn't cover is exactly what custom signals
are for: your pain points, your triggers, your product's disqualifiers. A good signal set
usually mixes both.

```
Signal 3 — icp_fit
  LibraryKey:  tech
  TechFilter:  --category crm
  DerivedFrom: icp_constraint
  ...
```

## Signal categories

Generate signals across three categories:

### `icp_fit` (4–6 signals)
Tests whether the company profile matches the ICP target.
- Employee count, industry, stage, geography, tech stack requirements
- These establish baseline fit — wrong profile = wrong prospect regardless of timing

### `urgency` (3–5 signals)
Tests whether a recent event creates buying pressure.
- **Must include explicit time bounds** — e.g. "in the last 6 months", "in the last 9 months"
- Never phrase as "Has the company ever..." — that returns stale data
- Based on buying triggers from the ICP
- A missing urgency signal is not a disqualifier — it means "right fit, wrong time" (−15 points)

### `buying_signal` (4–5 signals)
Tests whether the company shows evidence of the pain point.
- Derived from ICP pain points using the inverse framing above
- A missing buying signal means no evidence of pain → likely a poor fit
- **Never set as a disqualifier** — buying signals are bonuses, not hard requirements

## Signal format

Output each signal in this format (use `LibraryKey` instead of `Question` when a
Signal Library key covers the signal — see above):

```
Signal {N} — {category}
  Question:    {the exact research question to ask about a prospect company}
  AnswerType:  {boolean | number | open_text}
  DerivedFrom: {pain_point | buying_trigger | icp_constraint}
  SourceText:  {the exact pain point or trigger this is based on}
  Rationale:   {why this signal identifies a good prospect}

  Interpretation:
    [For boolean]
      positiveAnswer:   true / false   (which answer indicates a fit?)

    [For number]
      numberRange:      { min: X, max: Y }
      higherIsBetter:   true / false   (if outside range, which direction is better?)

    [For open_text]
      positiveKeywords: ["word1", "word2", ...]
      negativeKeywords: ["word1", "word2", ...]

    [All signals]
      isDisqualifier:   true / false   (wrong answer → prospect score = 0?)
      weight:           1 / 2 / 3      (1 = nice-to-have, 2 = important, 3 = critical)
```

## Disqualifier rules

**When to set `isDisqualifier: true`:**
- The product only works with a specific tech stack (requires NetSuite, Salesforce, AWS)
- The product is only relevant to a specific industry or geography
- The prospect would be a direct competitor

**When to NEVER set `isDisqualifier: true`:**
- `buying_signal` category — these are bonuses, never gatekeepers
- `urgency` category — timing signals never eliminate a prospect
- Any signal based on a "nice-to-have" criterion

## Weight guide

| Weight | Use when |
|---|---|
| **3 — critical** | Core fit requirement or strongest urgency signal (max 2–3 signals) |
| **2 — important** | Most signals should be here |
| **1 — nice-to-have** | Supplementary signals, low-confidence proxies |

## Scoring algorithm

Once signals are run against a prospect:

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

## Saber CLI — activating signals

Once the signal set is approved, use `create-company-signals` to activate them in Saber.

Library signals run by key against a domain:

```bash
saber signal funding --domain acme.com
saber signal tech --domain acme.com --category crm
```

Custom signals run as scheduled subscriptions against a list:

```bash
saber subscription create \
  --list <listId> \
  --name "<signal name>" \
  --question "<signal question>" \
  --answer-type boolean \
  --frequency weekly
```

Run one `subscription create` command per custom signal. The signal metadata (weight, category, interpretation rules) should be kept in conversation context — both `configure-scoring` (to materialize the model into native scoring rules) and `score-accounts` (when falling back to client-side ranking) consume it.

## Materializing the model with native scoring

The scoring algorithm above is portable — it works on any data source. To make Saber compute fit and urgency automatically as signals run, hand off to `configure-scoring`. It translates this signal set into a scoring profile.

Default mapping:

| Category here | `configure-scoring` dimension |
|---|---|
| `icp_fit` | `fit` |
| `buying_signal` | `urgency` |
| `urgency` | `urgency` |

Default point values: `points = weight × 10`. Disqualifiers go in `fit` with strongly negative `false` points. See [`_shared/scoring.md`](../_shared/scoring.md) for the full answer-type → point-values shape contract.

## Quality checks

Before finalising:

- [ ] No hand-written question duplicates a Signal Library key (`funding`, `mna`, `tech`, `open-jobs`, `firmographics`) — use the library signal instead
- [ ] Every urgency signal has an explicit time bound ("in the last 6 months" — not "recently" or "ever")
- [ ] No `buying_signal` has `isDisqualifier: true`
- [ ] No `urgency` signal has `isDisqualifier: true`
- [ ] At most 2–3 signals have `weight: 3` (reserve for truly critical signals)
- [ ] Each signal maps back to a specific pain point, trigger, or ICP constraint
- [ ] `open_text` signals have both `positiveKeywords` and `negativeKeywords` defined
- [ ] The signal set covers all three categories: `icp_fit`, `urgency`, `buying_signal`
- [ ] At least one signal uses the inverse pain point framing (looking for absence of a capability)

## Next steps

- Use `create-company-signals` to activate the signals against a Saber list
- Use `configure-scoring` to materialize this weighted model as a native scoring profile (fit + urgency auto-compute as signals fire)
- After signals run, use `score-accounts` to rank accounts by current scores
