---
name: extract-icp
description: >
  Research a company domain and extract a structured Ideal Customer Profile (ICP) —
  including target company profile, buying committee, pain points, buying triggers,
  and existing customers.
---

# Extract ICP

Use this skill to research a company and produce a structured ICP that can be used as
the foundation for signal generation, list building, and outreach personalisation.

## Input

Ask the user for:
- **domain** — the company's website domain (e.g. `acme.com`)
- **context** _(optional)_ — any targeting hint, e.g. "focus on NetSuite users" or "Series A SaaS only"

If the domain is already available from conversation context or from `saber org get`, use it directly and skip asking.

## Saber CLI check

Run `saber --help` to check if the Saber CLI is installed. If available, use it to supplement research:
```bash
saber signal --domain <domain> --question "Who are the target customers of this company?"
saber signal --domain <domain> --question "What pain points does this company's product solve?"
```

The CLI is optional — the skill works without it using web research.

## Step 1 — Research the company

Use web search, the company website, and any available tools to answer these questions:

```
1. What is their main product or service?
2. Who are their target customers? Describe the typical buyer.
3. What pain points do they solve? What were customers struggling with BEFORE buying?
4. What buying triggers lead companies to purchase? (e.g. new hire, funding round, expansion)
5. Name 5–10 customer companies they work with.
6. What is the typical customer company size? (employees and/or revenue)
7. What industries do their customers come from?
8. What is the buying committee? (who approves, who influences, who uses the product)
9. What is their pricing model and estimated deal size?
10. What is their sales motion? (self-serve / sales-assisted / enterprise)
```

**Best sources:**
- Company website — especially `/customers`, `/case-studies`, `/pricing`
- G2 / Capterra reviews — the "before" story in reviews is the most valuable
- LinkedIn job postings — the ideal candidate description often mirrors the buyer profile
- Case study PDFs and testimonials
- Saber CLI: `saber signal --domain <domain> --question "..."`

**What to look for in case studies and reviews:**
- The specific situation the customer was in *before* buying
- The exact words customers use to describe their problem
- What triggered them to look for a solution

## Step 2 — Extract the structured ICP

Output in this format:

```
ICP — {domain}

PRODUCT
  Summary: [1–2 sentence description of what the product does and who it's for]

TARGET COMPANY
  Employee range:  [min]–[max]
  Stage:           [startup / growth / enterprise / any]
  Revenue range:   [$X–$Y if known]
  Industries:      [2–5 specific industries, not broad categories like "tech"]
  Geography:       [countries or regions]

BUYING COMMITTEE
  Decision makers: [job titles — e.g. "CFO", "VP of Engineering"]
  Influencers:     [titles that evaluate but don't approve]
  End users:       [titles who use the product daily]

DEAL ECONOMICS
  Pricing model:   [SaaS / usage / per-seat / project / other]
  Estimated ACV:   [$X–$Y or "unknown"]
  Sales motion:    [self-serve / sales-assisted / enterprise]

PAIN POINTS
  (What customers LACKED before buying — use customer language when possible)
  1. [specific pain point]
  2. [...]
  ...

BUYING TRIGGERS
  (Events that create urgency or prompt a search for solutions)
  1. [trigger event]
  2. [...]
  ...

EXISTING CUSTOMERS
  (For lookalike targeting — list what you know)
  - {Company}: {industry}, ~{headcount}, {country}
  - [...]
```

## Key insight

> **Pain points describe what customers LACKED before buying.**
> Good prospects ALSO lack these things right now.

This inverted framing is the foundation of good signal generation:
- Pain point: "No in-house marketing expertise" → prospect **doesn't have** a marketing team
- Pain point: "Manual reconciliation errors" → prospect **lacks** financial automation
- Pain point: "Reporting takes days to produce" → prospect has a **small** ops/finance team

Don't look for companies that solved the problem. Look for companies that still have it.

## Quality checks

Before finalising:

- [ ] Pain points use specific, observable language (not "inefficiency" — say "manual Excel-based reconciliation")
- [ ] Buying triggers are events, not attributes (not "growing company" — say "raised Series B" or "hired new CFO")
- [ ] Employee range is specific, not "SMB" or "mid-market"
- [ ] Industries are specific (not "technology" — say "SaaS", "fintech", "healthcare tech")
- [ ] Decision maker titles are singular and job-title-exact (not "finance leaders" — say "CFO", "Controller")
- [ ] At least 3 named existing customers listed with industry and headcount if known

## Next step

Once the ICP is confirmed, use the `generate-signals` skill to turn it into a scored signal set ready for Saber.
