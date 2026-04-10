---
name: import-from-hubspot
description: >
  Pull a HubSpot company list, view, or segment into Saber as a target account
  or contact list, ready for signal activation.
---

# Import from HubSpot

Use this skill to pull companies or contacts from HubSpot into Saber as a list, so you can run signals against your existing CRM data.

## HubSpot MCP check

Before doing anything else, check if a HubSpot MCP is available by looking for HubSpot-related tools in your available tool list (tools with `hubspot` in the name for reading contacts, companies, or lists).

**If a HubSpot MCP is available:** proceed with the full workflow below.

**If no HubSpot MCP is detected:** inform the user and offer two options:
1. **Set up the HubSpot MCP** — guide them to install the HubSpot MCP server for Claude Code
2. **Manual CSV import** — ask the user to export their HubSpot list as a CSV and share it; then use `saber list company import` or `saber list contact create` to bring it into Saber

## Saber CLI check

Also verify the Saber CLI is available (`saber --help`). It is needed to create the Saber list.

**If not available:** inform the user and direct them to saber.app.

## Step 1 — Identify the HubSpot data to import

Ask the user what they want to import:
- A specific HubSpot **list** (static or active)?
- A **view** or **segment** filtered by properties (e.g. industry, lifecycle stage, owner)?
- All companies/contacts matching certain criteria?

Use the HubSpot MCP to browse available lists if the user isn't sure:

```
List all HubSpot company lists / contact lists
```

## Step 2 — Pull the records from HubSpot

Using the HubSpot MCP, fetch the companies or contacts from the selected list/view. For each record, collect:

**For companies:**
- Company name
- Domain / website
- Industry, size, country (if available)
- HubSpot company ID (for reference)

**For contacts:**
- Full name
- LinkedIn URL (if available — preferred for Saber)
- Email address
- Company name
- HubSpot contact ID

Paginate through the full result set if the list is large.

## Step 3 — Create the Saber list

**For a company list:**
```bash
saber list company create --name "<descriptive name, e.g. HubSpot - Tier 1 Accounts>"
```

If the companies have consistent attributes (industry, size, country), consider using those as filters instead so Saber can also discover similar companies:
```bash
saber list company create --name "<name>" --industry "<industry>" --country "<country>" --size "<size>"
```

**For a contact list:**
Create the list using the LinkedIn URLs from HubSpot:
```bash
saber list contact create --name "<name>" \
  --company-linkedin <url> \
  --company-linkedin <url> \
  [--title "<title>"]
```

## Step 4 — Confirm import

Show the user a summary:
- How many records were pulled from HubSpot
- How many were successfully added to Saber
- Any records that couldn't be matched (e.g. contacts without a LinkedIn URL or domain)

```bash
saber list company get <listId>
# or
saber list contact get <listId>
```

## Step 5 — Suggest next steps

Once the list is in Saber:
- Run `signal-discovery` if signal definitions don't exist yet
- Run `create-company-signals` or `create-contact-signals` to activate signal tracking against the imported list
- Run `score-accounts` after signals complete to prioritise the imported accounts
