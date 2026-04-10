---
name: market-map
description: >
  Map a target market — size, segments, key players, adjacent verticals, and entry
  points. Helps GTM teams understand the landscape before building lists or running signals.
---

# Market Map

Use this skill to build a structured map of a target market before you start prospecting
into it. The output gives you a clear picture of who's in the market, how it's segmented,
and where your best entry points are.

No CLI or special tools required. Works from web research alone. If the Saber CLI is
available, it can enrich the output with signal data on key segments.

## Input

Ask the user for:
- **Market or category** — e.g. "B2B SaaS companies selling to mid-market HR teams in Europe"
- **ICP context** _(optional)_ — pull from `extract-icp` if available
- **Intent** — what are they mapping this market for? (entering a new segment, building a TAM analysis, prioritising territories)

## Step 1 — Size and shape the market

Research to answer:

- How many companies roughly fit the target profile?
- What's the estimated TAM / SAM for this segment?
- Is this market growing, stable, or contracting?
- Is it early-stage (fragmented, low awareness) or mature (consolidated, competitive)?
- What geography does this market span?

**Best sources:**
- Industry analyst reports (Gartner, Forrester, IDC summaries)
- G2 category pages (number of products, number of reviews = market maturity signal)
- LinkedIn: search the target company type and check result counts
- YC, Crunchbase for funding activity as a growth proxy

## Step 2 — Identify and define segments

Break the market into meaningful segments — groups of companies that behave similarly
and are best reached the same way:

```
SEGMENTS

Segment 1 — [Name]
  Description:   [Who they are]
  Size:          [Estimated number of companies / percentage of market]
  Characteristics: [What makes this segment distinct — size, geography, tech stack, growth stage]
  Example companies: [3–5 real company names]
  Entry difficulty: [Easy / Medium / Hard — and why]

Segment 2 — [...]
```

Segment on dimensions that matter for GTM, not just firmographics:
- **By buyer** — who makes the decision? (e.g. CTO-led vs RevOps-led vs HR-led)
- **By stage** — early-stage companies behave differently from scale-ups
- **By tech stack** — already using [tool] vs greenfield
- **By motion** — product-led vs sales-led; inbound-heavy vs outbound-heavy
- **By geography** — regulatory, cultural, and competitive differences that affect buying

## Step 3 — Map key players

For each segment, identify the notable companies (targets, not competitors):

```
KEY PLAYERS BY SEGMENT

[Segment 1]
  Large (500+ employees):  [company names, countries, brief description]
  Mid-market (51–500):     [company names]
  Small (11–50):           [company names]
```

If the Saber CLI is available, build a list and run signals:
```bash
saber list company create --name "[Segment 1]" --industry "<industry>" --country "<country>" --size "<size>"
```

## Step 4 — Adjacent verticals

Identify markets that are adjacent to the core target — either as expansion targets
or as sources of lookalike companies:

```
ADJACENT VERTICALS

[Adjacent market 1]:
  Similarity to core:  [Why this is adjacent]
  Differences:         [What's different — buyer, tech stack, compliance, motion]
  Opportunity:         [Worth pursuing now / worth watching / out of scope]

[...]
```

## Step 5 — Entry point recommendations

Based on the map, recommend where to start:

```
RECOMMENDED ENTRY POINTS

Best first segment: [Name]
  Why: [Highest density of ICP-fit companies / easiest to reach / least competitive]

Second priority: [Name]
  Why: [...]

Hold for now: [Name]
  Why: [Too early / too competitive / needs a reference customer first]

SUGGESTED FIRST LIST
  [Segment, filters, estimated size]
  → Use `build-account-list` to activate this segment in Saber
```

## Step 6 — Suggest next steps

- Run `build-account-list` to activate the highest-priority segment
- Run `signal-discovery` to define signals specific to this market's buying triggers
- Run `competitive-intel` to understand who else is selling into this space
- Re-run this skill when entering a new geography or vertical
