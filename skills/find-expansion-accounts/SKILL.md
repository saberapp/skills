---
name: find-expansion-accounts
description: >
  Identify existing customers showing buying signals for upsell or cross-sell opportunities
  using the Saber CLI.
---

# Find Expansion Accounts

Use this skill to surface upsell and cross-sell opportunities within your existing customer base by running expansion-focused signals against a list of current accounts.

## Saber CLI check

Before doing anything else, check if the Saber CLI is installed by running `saber --help`.

**If not installed:** inform the user that this skill requires the Saber CLI (available at saber.app).

## Step 1 — Get the customer list

Ask the user how they want to provide their existing customer accounts:

**Option A — Existing Saber list:**
```bash
saber list company list
```
Ask which list contains current customers.

**Option B — Import from HubSpot:**
If customers are tracked in HubSpot (e.g. lifecycle stage = "Customer"), use the `import-from-hubspot` skill to pull them into Saber first, then return here.

**Option C — Manual list:**
If the user has a list of domains, create a new Saber list:
```bash
saber list company create --name "Existing Customers"
```
Then ask them to add companies via the Saber dashboard.

## Step 2 — Define expansion signals

Expansion signals are different from acquisition signals — they indicate readiness to buy *more* or to expand into adjacent products/seats. Work with the user to define 3–5 expansion-specific signal questions.

Common expansion signal types:

**Growth signals** (they're scaling and may need more):
- "Is this company actively hiring in roles that would use our product?"
- "Has this company raised funding in the last 6 months?"
- "Is this company expanding into new markets or geographies?"

**Problem signals** (they're experiencing pain the expansion product solves):
- "Is this company posting about [specific challenge your expansion product addresses]?"
- "Is this company hiring for a role that suggests they're outgrowing their current setup?"

**Intent signals** (they're researching adjacent solutions):
- "Is this company evaluating or recently adopted [complementary tool]?"

Also consider **at-risk signals** — if the goal is retention, not just expansion:
- "Is this company reducing headcount or announcing layoffs?"
- "Is the original buyer at this company still in their role?"

If signal definitions already exist in conversation context (from `signal-discovery`), check whether they're relevant for expansion or whether new ones are needed.

## Step 3 — Check credits and confirm

```bash
saber credits
```

Tell the user: "Running [N] signals against [M] accounts will use [N×M] credits." Ask them to confirm.

## Step 4 — Run expansion signals

For a full list, create subscriptions:
```bash
saber subscription create \
  --list <listId> \
  --name "<expansion signal name>" \
  --question "<expansion signal question>" \
  --answer-type boolean \
  --frequency monthly \
  --run-once
```

Create one subscription per signal question. Use `--run-once` to get results immediately.

## Step 5 — Retrieve and score results

```bash
saber subscription get <subscriptionId>   # for each expansion signal subscription
```

Score each customer account:
- +1 per positive expansion signal
- −1 per at-risk signal (if defined)
- Weight growth signals higher if the user's product is usage-based

## Step 6 — Present expansion opportunities

```
## Expansion Opportunities — [Customer List Name]

### High priority (2+ positive signals)
| Company | Domain | Signals | Top signal |
|---------|--------|---------|------------|
| Acme Corp | acme.com | 3/4 | Recently funded, hiring in sales |

### Watch list (1 positive signal)
| Company | Domain | Signal |
|---------|--------|--------|
| Beta Inc | beta.io | Expanding into EMEA |

### At-risk accounts
| Company | Domain | Risk signal |
|---------|--------|-------------|
| Gamma Ltd | gamma.io | Reducing headcount |
```

## Step 7 — Suggest next steps

- **High priority accounts:** use `write-outreach` with expansion-focused messaging — reference the specific growth signal as the reason for reaching out
- **Watch list accounts:** schedule signal re-runs in 4 weeks to track movement
- **At-risk accounts:** flag for the account management team and consider a proactive check-in
