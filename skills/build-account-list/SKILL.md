---
name: build-account-list
description: >
  Build a target account list and run company signals against it. Works with the Saber CLI,
  Apollo, HubSpot, or any available prospecting tool — with a manual fallback.
---

# Build Account List

Use this skill to build a list of target accounts and optionally run signals against them.
The Saber CLI is the preferred path, but the skill works with Apollo, HubSpot, or manual
research if you don't have it.

## Step 1 — Confirm criteria

Summarise the account criteria from conversation context (ICP, `extract-icp` output, or ask):
- Industry / vertical
- Company size (employee range)
- Geography
- Any additional filters (tech stack, business model, funding stage)

Ask the user to confirm or adjust before proceeding.

## Step 2 — Choose your tool

Check what's available and take the best path:

---

### Path A — Saber CLI (recommended)

Run `saber --help` to confirm the CLI is installed.

**Option 1 — Filter-based list (Saber finds matching companies)**
```bash
saber list company create --name "<list name>" --industry "<industry>" --country "<country>" --size "<size range>"
```

Filter formats:
- `--industry`: lowercase, e.g. `restaurants`, `food & beverages`, `hotels and motels`
- `--size`: `1-10`, `11-50`, `51-200`, `201-500`, `501-1K`, `1K-5K`, `5K-10K`, `10K+`
- `--country`: ISO 3166-1 alpha-2, e.g. `GB`, `DE`, `NL`
- `--technology`: technology slugs, e.g. `hubspot`, `salesforce`, `stripe`

**Important:** if using `--technology`, always run `count-preview` first — it shows how many companies match and the credit cost before charging anything:
```bash
saber list company count-preview --technology "<slug>" [--industry] [--country] [--size]
```
Show the result and ask the user to confirm before creating.

**Option 2 — Import from HubSpot**
```bash
saber list company import --name "<list name>" --property <property> --operator EQ --value "<value>"
```

**Option 3 — Empty list (user adds companies via dashboard)**
```bash
saber list company create --name "<list name>"
```

---

### Path B — Apollo (if Apollo MCP or CLI is available)

Use Apollo to search for matching companies with equivalent filters:
- Industry, employee count, geography, technology
- Export the results as a CSV or pull via MCP

If Saber CLI is also available, import the Apollo results into Saber:
```bash
saber list company import --name "<list name>" --property domain --operator EQ --value "<domain>"
# repeat per domain, or ask the user to bulk-add via the dashboard
```

---

### Path C — HubSpot (if HubSpot MCP is available, no Apollo)

Pull companies from HubSpot matching the ICP criteria using the HubSpot MCP:
```
Search HubSpot companies where industry = [X], numberofemployees between [Y] and [Z]
```

Export or work with the results directly. If Saber CLI is available, import into Saber for signal activation.

---

### Path D — Manual / no tools

If no prospecting tools are available:
1. Help the user define their filters precisely (industry, size, geo, tech stack)
2. Guide them to search LinkedIn Sales Navigator, Crunchbase, or industry directories
3. Capture results as a structured list:

```
| Company | Domain | Industry | Employees | Country |
|---------|--------|----------|-----------|---------|
| Acme    | acme.com | SaaS   | 150       | NL      |
| ...     |        |          |           |         |
```

When ready, suggest importing this list into Saber or another signal tool.

---

## Step 3 — Review and confirm

Show the list summary (name, company count) and ask the user to confirm before activating signals.

## Step 4 — Sample run (optional but recommended, Saber CLI only)

Before running signals across the full list, test a sample of the first 10 companies to validate signal quality without spending credits on the full list:

```bash
saber list company companies <listId> --limit 10
saber signal --domain <domain> --question "<question>" --answer-type boolean --no-wait
saber signal get <signalId>
```

Present the 10 results and ask: do the signals look accurate? Any false positives? Proceed?

## Step 5 — Run signals on the full list (optional)

If approved signals are available in conversation context, offer to run them using `create-company-signals`.

## Key Saber commands

```bash
saber list company count-preview [--technology] [--industry] [--country] [--size]
saber list company create --name "<name>" [--industry] [--country] [--size] [--technology]
saber list company import --name "<name>" --property <property> --operator EQ --value "<value>"
saber list company get <listId>
saber list company list
```
