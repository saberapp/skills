---
name: create-company-signals
description: >
  Activate company-level signal tracking. Runs signals via the Saber CLI if available;
  without it, outputs a research playbook and suggests alternative tracking methods.
---

# Create Company Signals

Use this skill to run company-level signal research against a list of target accounts.

The Saber CLI is the preferred path — it automates signal runs across your full list on a schedule.
Without it, this skill produces a research playbook you can run manually or via other tools.

## Before running signals (Saber CLI)

If the CLI is available, check credits before executing any command that will consume them:
```bash
saber credits
```
Tell the user: "Running [N] signals against [M] companies will use [N×M] credits." Ask them to confirm.

## Step 1 — Confirm signals and list

From conversation context, confirm:
- The approved signal questions to activate (from `generate-signals` or `signal-discovery`)
- The account list to run them against

To find a list ID:
```bash
saber list company list
```

## Step 2 — Run signals

---

### Path A — Saber CLI (full list, subscription mode)

**Option 1 — Run once (recommended for getting started)**
```bash
saber subscription create \
  --list <listId> \
  --name "<signal name>" \
  --question "<signal question>" \
  --answer-type boolean \
  --frequency monthly \
  --run-once
```
Creates the subscription, runs it immediately, then stops the schedule.

**Option 2 — Recurring schedule**
```bash
saber subscription create \
  --list <listId> \
  --name "<signal name>" \
  --question "<signal question>" \
  --answer-type boolean \
  --frequency weekly
```

Create one subscription per signal question.

**Spot-check a single company**
```bash
saber signal --domain <domain> --question "<question>" --answer-type boolean
```

Use `--no-wait` to fire multiple signals in parallel:
```bash
saber signal --domain <domain> --question "<question>" --no-wait
saber signal get <signalId>
```

---

### Path B — No Saber CLI (research playbook)

Output a structured research playbook the user or their team can run manually or via other tools:

```
SIGNAL RESEARCH PLAYBOOK — [List Name]

For each company in the list, answer these questions:

Signal 1: [Question]
  Category: [icp_fit / urgency / buying_signal]
  Weight: [1–3]
  How to find the answer:
    - LinkedIn: [specific search or filter]
    - Google: [suggested search string]
    - Apollo: [filter to apply]
    - News: [alert or keyword to track]

Signal 2: [Question]
  [...]

TRACKING TEMPLATE
  Use a spreadsheet with columns:
  | Company | Domain | [Signal 1] | [Signal 2] | ... | Score | Notes |
```

Also suggest:
- **Google Alerts** for trigger-based signals (funding, leadership changes, expansion news)
- **Apollo or LinkedIn Sales Nav** filters for ICP-fit signals (headcount, tech stack, hiring)
- **HubSpot custom properties** to track signal results per company if HubSpot MCP is available

---

## Step 3 — Review results (Saber CLI)

```bash
saber subscription get <subscriptionId>
```

When complete, present results. Companies where signals fired positively are highest priority for outreach.

After reviewing results, use `score-accounts` to rank the full list.

## Key Saber commands

```bash
saber subscription create --list <listId> --name "<name>" --question "<question>" [--answer-type] [--frequency] [--run-once]
saber subscription trigger <subscriptionId>
saber subscription get <subscriptionId>
saber subscription list
saber subscription start <subscriptionId>
saber subscription stop <subscriptionId>
saber signal --domain <domain> --question "<question>" [--answer-type] [--no-wait]
saber list company list
```
