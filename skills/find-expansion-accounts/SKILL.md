---
name: find-expansion-accounts
description: >
  Identify existing customers showing buying signals for upsell or cross-sell opportunities.
  Works with Saber, HubSpot, or any available customer data source.
---

# Find Expansion Accounts

Use this skill to surface upsell and cross-sell opportunities within your existing customer base
by running expansion-focused signals against a list of current accounts.

Works with or without the Saber CLI — the HubSpot MCP or a manual customer list are equally valid starting points.

## Step 1 — Get the customer list

Ask the user how their customer data is available:

---

### Path A — Saber CLI

Run `saber --help` to confirm the CLI is installed.

```bash
saber list company list
```

Ask which list contains current customers. If a customer list doesn't exist yet, offer to create one.

---

### Path B — HubSpot MCP (if available)

Pull customers directly using the HubSpot MCP:
```
Fetch HubSpot companies where lifecycle_stage = "customer"
```

Return company name and domain for each. If Saber CLI is available, create a Saber list from these domains. If not, work with the HubSpot data directly.

---

### Path C — Manual list

Ask the user to provide a list of customer domains or company names. Work with that list directly.

---

## Step 2 — Define expansion signals

Expansion signals indicate readiness to buy more or expand into adjacent products/seats.
Work with the user to define 3–5 expansion-specific signal questions.

**Growth signals** (they're scaling and may need more):
- "Is this company actively hiring in roles that would use our product?"
- "Has this company raised funding in the last 6 months?"
- "Is this company expanding into new markets or geographies?"

**Problem signals** (they're experiencing pain the expansion product solves):
- "Is this company posting about [specific challenge your expansion product addresses]?"
- "Is this company hiring for a role that suggests they're outgrowing their current setup?"

**Intent signals** (they're researching adjacent solutions):
- "Is this company evaluating or recently adopted [complementary tool]?"

**At-risk signals** (if the goal is retention, not just expansion):
- "Is this company reducing headcount or announcing layoffs?"
- "Is the original buyer at this company still in their role?"

If signal definitions already exist in conversation context, check whether they're relevant for expansion or whether new ones are needed.

## Step 3 — Run expansion signals

### With Saber CLI

Check credits first:
```bash
saber credits
```
Tell the user: "Running [N] signals against [M] accounts will use [N×M] credits." Ask them to confirm.

Then create subscriptions:
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

### Without Saber CLI

For each customer and each expansion signal question, research manually or via available tools:
- Web search for recent news (funding, hiring, expansion, layoffs)
- HubSpot MCP: check custom properties or notes for relevant activity
- LinkedIn: check company updates and job postings

Record findings in a structured table:

```
| Company | Growth signal | Problem signal | Intent signal | At-risk signal |
|---------|--------------|----------------|---------------|----------------|
| Acme    | ✓ Hiring     | —              | ✓ Eval HubSpot | —             |
```

## Step 4 — Score and present expansion opportunities

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

## Step 5 — Suggest next steps

- **High priority accounts:** use `write-outreach` with expansion-focused messaging — reference the specific growth signal as the reason for reaching out
- **Watch list accounts:** re-run signals in 4 weeks to track movement
- **At-risk accounts:** flag for the account management team and consider a proactive check-in
- Use `deal-coaching` for any expansion account already in a conversation
