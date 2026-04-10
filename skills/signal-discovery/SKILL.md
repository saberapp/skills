---
name: signal-discovery
description: >
  Guided entry point for defining your ICP and buying signals — runs extract-icp
  then generate-signals in sequence. Start here before creating signals or building lists.
---

# Signal Discovery

Use this skill as the starting point for signal-based prospecting. It loads your organisation
context, then runs `extract-icp` and `generate-signals` in sequence to produce a complete,
scored signal set ready to activate in Saber.

## Saber CLI check

Run `saber --help` to check if the Saber CLI is installed.

**If the CLI is available:** proceed with the full workflow below.

**If not installed:** inform the user that this skill works best with the Saber CLI
(available at saber.app), then offer two options:
1. **Install the CLI** — once installed, restart this skill
2. **Continue without the CLI** — skip `saber org get` and ask for org context manually;
   the ICP extraction and signal generation steps work fully without the CLI

## Step 1 — Load organisation context

**With CLI:** run `saber org get`
- If the profile contains name, website, and description → use them directly, skip asking.
- If the profile is empty or missing key fields → ask the user to fill in the gaps, then persist:
  ```bash
  saber org update --name "..." --general "..." --products "..." --use-cases "..." --value-prop "..."
  ```

**Without CLI:** ask the user:
- What does your company do?
- Who do you sell to?
- What problem do you solve?

Do not ask questions already answered by available context (e.g. CLAUDE.md).

## Step 2 — Extract the ICP

Use the `extract-icp` skill with the company's domain.

This produces a structured ICP covering: target company profile, buying committee,
deal economics, pain points, buying triggers, and existing customers.

Keep the ICP output in conversation context for the next step.

## Step 3 — Generate signals

Use the `generate-signals` skill with the ICP from Step 2.

This produces 12–15 research signals across three categories:
- `icp_fit` — does the company profile match?
- `urgency` — did a recent trigger event happen?
- `buying_signal` — is there evidence of the pain?

Each signal includes answer type, weight (1–3), disqualifier flag, and interpretation rules
needed for automatic scoring.

Confirm the signal set with the user before handing off. Adjust any signals that don't fit.

## Step 4 — Hand off

Keep the agreed ICP and signal definitions in conversation context.

Once signals are approved, tell the user their next steps:
- To build a target list: use `build-account-list` or `build-contact-list`
- To activate company signals: use `create-company-signals`
- To activate contact signals: use `create-contact-signals`
- After signals run: use `score-accounts` to rank accounts using the weighted scoring model
