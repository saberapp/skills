---
name: competitive-intel
description: >
  Map the competitive landscape for a target market — identify competitors, compare
  positioning, surface common objections, and build battle cards for your sales team.
---

# Competitive Intel

Use this skill to map who else is competing for the same buyer, how they're positioned,
and how to win deals when they come up. The output is a battle-card-ready competitive
overview your reps can use in active deals.

No CLI or special tools required. Works from web research alone.

## Input

Ask the user for:
- **Your product / company** — what you sell and who you sell to (or use ICP context from `extract-icp`)
- **Known competitors** _(optional)_ — names the team already encounters in deals
- **Focus** _(optional)_ — "we keep losing to X" or "we're entering a new segment and don't know the landscape"

## Step 1 — Identify the competitive set

Research who else serves this buyer:

**Direct competitors** — same category, same buyer, similar price point
**Adjacent alternatives** — different category but competes for the same budget or mindset (e.g. "build it in-house", spreadsheets, agencies)
**Emerging entrants** — newer players gaining traction in the space

**Best sources:**
- G2 / Capterra "alternatives to [your product]" pages
- "Compared to" mentions in G2 reviews of your product
- LinkedIn job postings at target companies — what tools do they list as requirements?
- Your own lost deal data — ask the user what names come up
- Product Hunt, Y Combinator recent batches (for emerging players)

## Step 2 — Profile each competitor

For each competitor, research:

```
COMPETITOR — [Name]

Overview:      [1–2 sentences: what they do and who they target]
Founded:       [year / stage if known]
Pricing:       [model and rough range — free tier, per-seat, usage-based, enterprise]
Target buyer:  [who they sell to — title, company size, industry]
Key strengths: [what customers genuinely love — pull from G2 reviews]
Key weaknesses:[what customers complain about — pull from G2 reviews]
Positioning:   [how they describe themselves — pull from homepage headline]
Typical deal:  [SMB / mid-market / enterprise, sales-assisted / self-serve]
```

## Step 3 — Build battle cards

For each significant competitor, produce a battle card:

```
BATTLE CARD — [Competitor] vs [Your Product]

WHEN THEY COME UP
  [In what deal stage or context does this competitor appear?]
  [What does the buyer usually say when they bring them up?]

YOUR STRENGTHS IN THIS MATCHUP
  1. [Specific advantage — back it with evidence if possible]
  2. [...]

THEIR STRENGTHS (be honest)
  1. [Where they genuinely win — don't dismiss this]
  2. [...]

COMMON OBJECTIONS AND RESPONSES
  "[Exact objection the prospect raises]"
  → [Response that acknowledges the concern, then reframes]

  "[Another objection]"
  → [Response]

TRAP QUESTIONS TO ASK
  (Questions that surface your advantages without sounding like a pitch)
  - "[Question that reveals a gap in the competitor's approach]"
  - "[Question that gets the buyer to articulate what matters to them]"

PROOF POINTS
  - [Customer name / use case that directly counters competitor's strength]
  - [Data point or quote that reinforces your position]

WHEN TO WALK AWAY
  [Signs that this deal is firmly in the competitor's camp — save everyone's time]
```

## Step 4 — Competitive positioning summary

Produce a one-page landscape overview:

```
COMPETITIVE LANDSCAPE — [Market / Category]

MARKET OVERVIEW
  [2–3 sentences on the market: how mature, how consolidated, where it's heading]

COMPETITIVE TIERS
  Tier 1 (direct / primary threats): [names]
  Tier 2 (adjacent / indirect):      [names]
  Tier 3 (emerging / niche):         [names]

YOUR POSITION
  [Where you sit in this landscape — be specific, not just "we're the best"]
  [What segment or use case you own most clearly]
  [Where you're weakest relative to the field]

MARKET DYNAMICS TO WATCH
  - [Trend or shift that could affect the competitive balance]
  - [Emerging player worth tracking]
```

## Step 5 — Suggest next steps

- Use battle cards in `deal-coaching` when a specific competitor comes up in a deal
- Feed "language competitors use" into `write-outreach` to differentiate messaging
- Add competitive positioning to the persona profile from `persona-research`
- Re-run this skill quarterly — competitive landscapes shift fast
