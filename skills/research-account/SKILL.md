---
name: research-account
description: >
  Deep research on a single company — signals, hiring, news, tech stack, and LinkedIn presence.
  Uses the Saber CLI for signal data and any available MCP tools for broader research.
---

# Research Account

Use this skill to build a comprehensive picture of a target company before reaching out or prioritising them for outreach.

## Goal

Produce a research brief covering: company overview, recent news and events, hiring signals, tech stack, LinkedIn presence, and any Saber signal results — ready to inform outreach or qualification decisions.

## Step 1 — Identify the target

Ask the user for the company name or domain if not already in conversation context.

## Step 2 — Gather available tools

Check what research tools are available:

**Saber CLI** (`saber --help`): can run ad-hoc signals against the domain for structured buying intent data.

**MCP tools**: scan available tools for:
- Web search (Brave, Perplexity, Tavily, or similar)
- LinkedIn MCP
- HubSpot MCP (to check if they're already a known contact/account)
- Any other research or enrichment tools

Note what's available — use everything you can find.

## Step 3 — Research the company

Run these in parallel where possible:

### Company overview
- What do they do, what market, what size, what business model
- Use web search MCP if available, otherwise rely on conversation context or ask the user

### Recent news and events
Search for: funding rounds, acquisitions, leadership changes, product launches, layoffs, expansion announcements, press coverage. Focus on the last 6–12 months.

### Hiring signals
Search for open roles on LinkedIn or their careers page. Note:
- Volume of open roles (growth vs. contraction)
- Roles in sales, RevOps, or buyer-relevant departments
- Senior hires that indicate strategic shifts

### Tech stack
Check job descriptions, BuiltWith mentions in search results, or G2/Capterra reviews for technology signals — e.g. what CRM, data stack, or infra they run.

### LinkedIn presence
If a LinkedIn MCP is available, look up the company page:
- Employee count and recent growth
- Recent company posts or announcements
- Key decision-makers and their recent activity

### Saber signal results
If the Saber CLI is available, check for existing signal data on this domain:

```bash
# Check if signals have already been run for this domain
saber signal --domain <domain> --question "Is this company actively hiring in sales or revenue roles?" --answer-type boolean
```

If pre-defined signal subscriptions exist for a list containing this company, retrieve those results:
```bash
saber subscription list
saber subscription get <subscriptionId>
```

### HubSpot check
If a HubSpot MCP is available, look up whether this company already exists as a contact or deal in HubSpot — note the current stage and any logged activity.

## Step 4 — Present the research brief

Structure the output as a brief:

```
## [Company Name] — Research Brief

**Overview:** 2–3 sentence summary of what they do and why they matter.

**Recent signals:**
- [Key finding from news/events]
- [Key finding from hiring]
- [Saber signal results if available]

**Tech stack:** [Notable tools]

**LinkedIn:** [Employee count, growth trend, notable activity]

**HubSpot status:** [Known / not known; deal stage if applicable]

**Outreach angle:** 1–2 sentence recommendation for how to approach them based on the research.
```

## Step 5 — Suggested next steps

After presenting the brief, suggest:
- Use `write-outreach` to draft a personalised message using this research
- Use `qualify-inbound` if you need a formal signal-based qualification score
- Use `enrich-contacts` to find specific contact details for the people to reach out to
