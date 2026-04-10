---
name: sync-to-hubspot
description: >
  Push Saber signal results back to HubSpot as contact or company properties,
  keeping your CRM up to date with the latest intent data.
---

# Sync to HubSpot

Use this skill to write Saber signal results back to HubSpot, so sales reps can see intent data directly on contact and company records without leaving their CRM.

## HubSpot MCP check

Before doing anything else, check if a HubSpot MCP is available by looking for HubSpot-related tools in your available tool list (e.g. tools with `hubspot` in the name for updating contacts, companies, or properties).

**If a HubSpot MCP is available:** proceed with the full workflow below.

**If no HubSpot MCP is detected:** inform the user and offer two options:
1. **Set up the HubSpot MCP** — guide them to install the HubSpot MCP server for Claude Code, which will allow direct CRM writes
2. **Export for manual import** — format the signal results as a CSV that can be imported into HubSpot via the CRM's import tool

## Saber CLI check

Also check if the Saber CLI is available (`saber --help`). It is needed to fetch signal results.

**If not available:** ask the user to provide signal results directly (paste output or CSV from the Saber dashboard).

## Step 1 — Identify what to sync

Ask the user:
- Which signal subscription(s) results should be synced?
- Should results be written to **company** records, **contact** records, or both?

Fetch the subscriptions:
```bash
saber subscription list
saber subscription get <subscriptionId>
```

## Step 2 — Map signals to HubSpot properties

For each signal question, decide how to represent it in HubSpot. Common mappings:

| Signal | HubSpot property type | Example property name |
|--------|-----------------------|-----------------------|
| Boolean signal (yes/no) | Checkbox or single-line text | `saber_hiring_signal` |
| Signal result with detail | Multi-line text | `saber_signal_notes` |
| Signal run date | Date | `saber_last_signal_date` |
| Overall score (from `score-accounts`) | Number | `saber_intent_score` |

Ask the user if these HubSpot properties already exist or need to be created. If they need to be created, use the HubSpot MCP to create them first.

Recommend using a consistent prefix (e.g. `saber_`) for all Saber-sourced properties so they're easy to filter and report on in HubSpot.

## Step 3 — Match Saber companies/contacts to HubSpot records

For each company in the signal results, find the matching HubSpot record:
- Search HubSpot by company domain first (most reliable)
- Fall back to company name if domain doesn't match

For contact records: search by LinkedIn URL or email address.

Note any records that don't have a HubSpot match — present these to the user at the end.

## Step 4 — Write signal results to HubSpot

For each matched record, update the mapped properties using the HubSpot MCP:
- Write the signal result (boolean or text)
- Write the date the signal was run
- Write any summary notes from the signal result

Confirm each write succeeded. If any writes fail, note them and continue — don't abort the whole sync.

## Step 5 — Summary

Present a sync summary:
```
## HubSpot Sync Complete

✓ Updated: [N] company records
✓ Updated: [N] contact records
⚠ No HubSpot match found: [list of unmatched companies]
✗ Failed writes: [list of any errors]

Properties written: saber_hiring_signal, saber_funding_signal, saber_last_signal_date
```

Suggest next steps:
- Create a HubSpot list filtered by `saber_intent_score > 7` to build a prioritised outreach view
- Set up a HubSpot workflow triggered by `saber_hiring_signal = true` to auto-enroll high-intent accounts in a sequence
