---
name: build-contact-list
description: >
  Build a target contact list and run contact-level signals against them. Works with
  the Saber CLI, Apollo, LinkedIn, HubSpot, or manual research.
---

# Build Contact List

Use this skill to build a list of target contacts and optionally run signals against them.
The Saber CLI is the preferred path, but the skill works with Apollo, LinkedIn, HubSpot,
or manual research if you don't have it.

## Step 1 — Confirm buyer persona

Summarise the contact criteria from conversation context (ICP, `persona-research`, or ask):
- Job title(s) and seniority
- Department
- Source account list (if targeting contacts at specific companies)

Ask the user to confirm or adjust before proceeding.

## Step 2 — Choose your tool

Check what's available and take the best path:

---

### Path A — Saber CLI (recommended)

Run `saber --help` to confirm the CLI is installed.

If a target account list exists, get company LinkedIn URLs from it first:
```bash
saber list company companies <listId>
```

Then create the contact list:
```bash
saber list contact create --name "<list name>" \
  --company-linkedin <linkedin-url> \
  --company-linkedin <linkedin-url> \
  --title "<title>"
```

`--company-linkedin` is required. `--title` is repeatable for multiple titles.

---

### Path B — Apollo (if Apollo MCP or CLI is available)

Use Apollo to search contacts with matching criteria:
- Title, seniority, department, company filters
- Export results as CSV or pull via MCP

If Saber CLI is also available, the contact list can be imported into Saber via the dashboard for signal activation.

---

### Path C — HubSpot (if HubSpot MCP is available)

Pull contacts from HubSpot matching the persona:
```
Search HubSpot contacts where jobtitle contains [title], company.numberofemployees between [Y] and [Z]
```

Work with the results directly. If Saber CLI is available, use LinkedIn URLs from HubSpot to import into Saber.

---

### Path D — LinkedIn search (manual)

If no tools are available:
1. Guide the user through a Boolean LinkedIn search for the target persona
2. Or describe which accounts and titles to search
3. Capture results in a structured format:

```
| Name | Title | Company | LinkedIn URL | Email (if known) |
|------|-------|---------|--------------|-----------------|
| ...  |       |         |              |                 |
```

Suggest using `enrich-contacts` to add email and phone once the list is built.

---

## Step 3 — Review and confirm

Show the list summary and ask the user to confirm before activating signals.

## Step 4 — Sample run (optional but recommended, Saber CLI only)

Before running signals across the full list, test 3–5 contacts the user already knows to validate signal accuracy without spending credits on the full list.

For each contact:
```bash
saber signal --profile <linkedin-url> --question "<question>" --answer-type boolean --no-wait
saber signal get <signalId>
```

Present results and ask: do the signals look accurate? Any false positives? Proceed?

## Step 5 — Run signals on the full list (optional)

If approved contact signals are available in conversation context, offer to run them using `create-contact-signals`.

## Step 6 — Prioritise

After signals complete, present results ranked by intent so the user can sequence outreach.

## Key Saber commands

```bash
saber list contact create --name "<name>" --company-linkedin <url> [--title] [--keyword] [--country]
saber list contact get <listId>
saber list contact list
```
