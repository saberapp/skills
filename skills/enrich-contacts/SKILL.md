---
name: enrich-contacts
description: >
  Find and add contact information (email, phone) to a Saber contact list using available
  enrichment providers such as Apollo or Cognism. Detects available MCPs and CLIs automatically.
---

# Enrich Contacts

Use this skill to look up email addresses and phone numbers for contacts in a Saber contact list, using whatever contact enrichment provider is available in the user's environment.

## Goal

For each contact in a list, find their email address and/or phone number using an available enrichment provider, and surface those results to the user.

## Step 1 — Get the contact list

**If the Saber CLI is available** (`saber --help` works):

Ask the user for the list ID, or help them find it:
```bash
saber list contact list
```

Then fetch the contacts:
```bash
saber list contact contacts <listId>
```

This returns contacts with name, LinkedIn URL, and any metadata Saber has. Note the names and LinkedIn URLs — these are what you'll pass to the enrichment provider.

**If the Saber CLI is not available:**

Ask the user to export the contact list as a CSV from the Saber dashboard and share it in the conversation. The CSV will contain contact names and LinkedIn URLs.

## Step 2 — Detect enrichment provider

Check for available contact enrichment providers in the following order:

### 2a — MCP tools

Look through your available tools for any contact enrichment MCP servers. Providers that commonly offer MCP servers include:

- **Apollo** — look for tools with `apollo` in the name (e.g. people search, contact lookup)
- **Cognism** — look for tools with `cognism` in the name
- **Hunter.io** — look for tools with `hunter` in the name (email finder)
- **Clearbit** — look for tools with `clearbit` or `enrichment` in the name
- **Clay** — look for tools with `clay` in the name
- **Lusha** — look for tools with `lusha` in the name
- **ZoomInfo** — look for tools with `zoominfo` in the name
- **People Data Labs (PDL)** — look for tools with `pdl` or `people-data-labs` in the name

If any enrichment MCP tools are found, proceed to Step 3 using those tools.

### 2b — CLI tools

If no MCP is detected, check for CLI tools:
```bash
apollo --help 2>/dev/null && echo "apollo found"
hunter --help 2>/dev/null && echo "hunter found"
pdl --help 2>/dev/null && echo "pdl found"
```

If a CLI is found, proceed to Step 3 using that CLI.

### 2c — No provider detected

If neither an MCP nor a CLI is found, skip to **Step 4 — No provider found**.

## Step 3 — Enrich contacts

Once a provider is detected, enrich each contact. For each person in the list:

1. Use the provider's name + LinkedIn URL (preferred) or name + company to look up their contact info
2. Collect the results: email address, phone number, and any additional data the provider returns
3. Present results in a clear table as you go, or as a batch summary at the end

**Pacing:** If the list is longer than 20 contacts, ask the user if they want to process all at once or in batches. Some providers have rate limits — process sequentially if unsure.

**What to surface per contact:**
- Full name
- Email address (and confidence score if available)
- Phone number (if available)
- LinkedIn URL (echo back for reference)

**If a lookup fails** for a specific contact (no result found or an error), note it clearly and continue to the next.

## Step 4 — No provider found

If no enrichment provider was detected, ask the user:

> "I couldn't find a contact enrichment provider in your environment. Do you have a service you use for finding contact information — such as Apollo, Cognism, Hunter.io, Clearbit, Clay, Lusha, or ZoomInfo?"

**If they name a provider**, help them connect it:

- **Apollo**: Apollo offers an MCP server and an API. Ask if they have an API key. If yes, guide them to install the Apollo MCP server so it's available in Claude Code. If no, direct them to apollo.io to sign up.
- **Cognism**: Ask if they have API access. Guide them to check whether a Cognism MCP server is available, or use their API directly with a script.
- **Hunter.io**: Has a simple REST API and CLI. Guide them to install the `hunter` CLI or call the API with `curl`.
- **Clay**: Clay is a data enrichment platform. Ask if they have a Clay table set up — they can export enriched data from Clay as CSV and share it in the conversation.
- **Other providers**: Ask if they have an API key. Offer to help write a simple script to call the provider's API for each contact.

**If they have no enrichment service**, explain the options:

> "To enrich contacts at scale you'll need a data provider. Here are the most common options:
> - **Apollo** (apollo.io) — good free tier, strong for B2B emails and LinkedIn enrichment
> - **Hunter.io** — focused on email finder, simple API, generous free tier
> - **Cognism** — enterprise-grade, strong in EMEA, phone numbers and GDPR compliance
> - **Clay** — flexible enrichment platform that chains multiple data sources
>
> Once you have a provider set up, come back to this skill and it will use it automatically."

## Output

Present the final results as a markdown table:

| Name | Email | Phone | LinkedIn |
|------|-------|-------|----------|
| Jane Doe | jane@example.com | +1 555 123 4567 | linkedin.com/in/janedoe |
| ... | | | |

If the user wants to import the enriched data back into Saber or another tool, offer to format the output as CSV.
