---
name: outreach-workflow
description: >
  End-to-end guided workflow — take one prospect domain, run your saved research signals,
  find named contacts on the buying committee, and write a hyper-personalised cold email
  and LinkedIn DM grounded in the signal answers. First run defines the source-company
  ICP, contact-search targets, and ≤5 weighted signals (saved to disk); every subsequent
  run reuses them. Use when you want full SDR research-and-write for a single prospect,
  not write-outreach in isolation.
---

# Outreach Workflow

A guided per-prospect SDR helper with two phases:

- **Setup** (runs once per source company). Define the ICP, contact-search targets, and up to 5 research signals. Save to disk.
- **Outreach** (runs every time). Take one prospect domain, run the saved signals, find named contacts, write hyper-personalized cold email + LinkedIn DM.

The unit of work is **one prospect at a time**. List-building, scoring, and campaign management are out of scope — see `prospecting-workflow` for those.

**Portability — only the Saber CLI is required.** Anyone with `saber` installed and authenticated (`saber auth login`) can run this skill. There are no API keys to manage and no project-local files. The only filesystem footprint is per-source config JSON under `~/.saber-skills/outreach-workflow/configs/`.

---

## Stage map

```
Start                 → choose source (saved / new / redefine)

Setup (per source, once)
  1. Source           → pick the source domain
  2. Research         → auto-research the source via Saber CLI
  3. ICP              → confirm or refine the structured ICP
  4. Committee        → confirm contact-search titles + keyword
  5. Signals          → generate ≤5 weighted research signals
  6. Save             → persist ~/.saber-skills/outreach-workflow/configs/<source>.json

Outreach (per prospect, every time)
  1. Prospect         → pick a single prospect domain
  2. Research         → run signals + contact search in parallel
  3. Results          → print signal table + contact shortlist
  4. Write            → pick contact, draft cold email + LinkedIn DM
```

Stage headers printed in chat use the natural names — `╔══ Setup · Signals ══╗`, `╔══ Outreach · Prospect ══╗` — never letter+number codes.

---

## Answer-type policy

Use the answer type that lets Claude **read structured fields** instead of parsing narrative prose. The Saber CLI's `--answer-type` flag accepts: `boolean`, `number`, `open_text`, `list`, `percentage`, `currency`, `url`. (`json_schema` is listed but the CLI does not accept an output-schema flag — avoid it for portability.)

| Type | Use when |
|---|---|
| `boolean` | Pure yes/no, no follow-up detail needed. |
| `number` | Single numeric value (employee count, ARR estimate). |
| `url` | Single URL — e.g. a company's LinkedIn page or a job-posting URL. |
| `list` | Array of same-type items — e.g. enumerate which pricing modes a company uses. |
| `percentage` / `currency` | Bounded numeric values with a unit. |
| `open_text` | Compound answers with multiple fields per item (e.g. "for each new finance hire, return name + title + start date + LinkedIn"). Frame the question with explicit per-item field names. |

**Bias toward `boolean` and `list`** when the answer space is enumerable. Reach for `open_text` only when each item has nested per-item detail.

---

## Config storage

Per-source-company configs live at:

```
~/.saber-skills/outreach-workflow/configs/<source-domain>.json
```

The directory is created on first save. It lives outside the plugin install path so configs survive plugin updates.

Schema:
```json
{
  "sourceDomain": "example.com",
  "savedAt": "YYYY-MM-DD",
  "icp": {
    "summary": "1–2 sentence product + buyer description",
    "targetCompany": { "size": "...", "industries": [...], "geography": "..." },
    "buyingCommittee": { "decisionMakers": [...], "influencers": [...], "endUsers": [...] },
    "painPoints": ["..."],
    "buyingTriggers": ["..."],
    "namedCustomers": [{ "company": "...", "industry": "...", "size": "...", "geo": "..." }]
  },
  "contactSearch": {
    "titles": ["CFO", "Head of Finance", "VP Engineering", "..."],
    "keyword": "single string — used as fallback only",
    "rationale": "why these titles map to the buying committee"
  },
  "signals": [
    {
      "id": 1,
      "category": "fit | potential | urgency",
      "question": "exact question to ask Saber",
      "answerType": "boolean | number | open_text | list | url | percentage | currency",
      "weight": 1 | 2 | 3,
      "interpretation": "what a positive answer looks like (one line — e.g. 'modes contains usage_based|credit_based|outcome_based|custom_enterprise')"
    }
    // up to 5
  ]
}
```

A worked example lives at `configs/_example.json` in this skill folder — read it for shape, don't copy it as a starting point (the values are placeholders).

Categories:
- **fit** — does the company match the target profile? (industry, size, geography, pricing shape)
- **potential** — is the pain present? (inverse pain points: "uses an inferior tool we replace", "lacks a capability we provide")
- **urgency** — is *now* the right time? (recent triggers: hire, raise, launch — always time-bounded with explicit windows)

---

## Start — explicit source choice (always)

**Always open the run with `AskUserQuestion`.** Don't infer the mode from filesystem state and don't ask for a domain first — the user can't tell whether a domain they type is "set up new source" or "research as prospect for current source", so make the choice explicit upfront.

Print the header:
```
╔══ Start ══╗
```

### How to build the options

1. List the configs at `~/.saber-skills/outreach-workflow/configs/*.json`. For each, read `sourceDomain` and `savedAt`.
2. Build the AskUserQuestion options dynamically:
   - One option per existing saved source (label: `"<domain> (saved <YYYY-MM-DD>)"`, description: *"Use saved config — go straight to prospect research"*).
   - One option `"Set up a new source company"` (description: *"Run Setup — define ICP, contact targets, and signals, then save"*).
   - If the user has at least one saved source, add `"Redefine an existing source"` (description: *"Re-run Setup, overwriting one of the saved configs"*).
3. AskUserQuestion has a hard 4-option cap. If there are more than 3 saved sources:
   - Show the 3 most recently-saved + the "Set up new" option.
   - Tell the user in the question text that they can pick `Other` and type any saved domain to use it.

If **zero** configs exist, skip the AskUserQuestion (the choice is degenerate). Announce *"No saved profiles found — running Setup."* and proceed.

### Once the user picks

| Selection | Branch |
|---|---|
| An existing saved source | Load that config silently. Print *"Using saved config for `<source>`."* Jump to **Outreach · Prospect**. |
| "Set up a new source company" | Ask for the new source domain. Run **Setup** end-to-end. Save. Continue to Outreach for the first prospect. |
| "Redefine an existing source" | Follow up with a second AskUserQuestion to choose which saved source to redefine. Re-run **Setup**, overwriting that config. |
| `Other` with free-text | Interpret intent: a bare domain → "Set up new" with that domain (or, if it matches a saved source, switch to it). A `redefine X` phrase → redefine. Anything else → ask for clarification. |

### Redefine escape hatches (mid-run)

In addition to the Start choice, the user can say any of these at any point to update the saved config of the **currently loaded source** without re-running the whole Setup:

| Phrase | Behavior |
|---|---|
| `redefine icp` | Re-run Setup · Research + Setup · ICP, keep Committee and Signals as-is. Re-save. |
| `redefine contacts` | Re-run Setup · Committee only, keep ICP and Signals. Re-save. |
| `redefine signals` | Re-run Setup · Signals only, keep ICP and Committee. Re-save. |
| `redefine all` / `start over` | Re-run all of Setup. Re-save (overwrite). |
| `switch source` | Re-open the Start choice without ending the run. |

After a partial redefine, continue back into whatever the user was doing (usually Outreach for the current prospect).

---

## Setup — define your source company (one-time)

Walk the user through the six stages below. Pause for confirmation between every stage.

### Setup · Source
Already collected at Start (or earlier from context). If not, ask now.

### Setup · Research
Run the existing `extract-icp` skill via the Skill tool with the source domain, OR fire targeted Saber CLI questions in parallel. Marketing copy alone gives a curated picture — for B2B/API-first products, the developer docs reveal the *actual* product shape, customer profile, and pricing dimensions, so always include the dev-docs question (#7 below).

```bash
saber signal --yes --domain <source> --question "What is the main product or service?" --answer-type open_text --json
saber signal --yes --domain <source> --question "Who are the target customers?" --answer-type open_text --json
saber signal --yes --domain <source> --question "What pain points does the product solve?" --answer-type open_text --json
saber signal --yes --domain <source> --question "What buying triggers prompt customers to buy?" --answer-type open_text --json
saber signal --yes --domain <source> --question "Name 5–10 named customers" --answer-type list --json
saber signal --yes --domain <source> --question "Who is in the buying committee at customer companies, including decision makers, influencers, and end users?" --answer-type open_text --json
saber signal --yes --domain <source> --question "Describe this company's developer and API surface. What APIs, SDKs, key endpoints, integration partners, and authentication methods do they expose? What use cases do their code examples and API docs reveal about typical customer implementations?" --answer-type open_text --json
```

Synthesize all seven answers into the ICP shape under "Config storage" above. The developer-docs answer typically reveals tighter ICP filters than the marketing answers (e.g. "their API has multi-entity invoicing primitives" → ICP must include multi-entity buyers; "their SDK ingests metering events" → ICP must include usage-based businesses). If the source has no developer docs (rare for B2B SaaS, common for service companies), the dev-docs question returns a low-confidence "none" — itself a useful ICP datapoint about the type of business this is.

Don't tell Saber where to look — Saber routes to dev docs internally based on the question content. The question describes *what* to find (API surface, code-example use cases, integration partners), not *where* to find it.

### Setup · ICP
Print a tight 6–10 line summary:
```
ICP — <source>
  Product:     <one sentence>
  Buyer:       <titles>, at <industries>, <size>
  Geo:         <countries>
  Pain points: <up to 4 short bullets>
  Triggers:    <up to 4 short bullets>
```
Ask: *"Look right? Anything to refine?"* Iterate until the user confirms.

### Setup · Committee
From the buying committee, derive a list of titles to feed Saber's contact search in Outreach. Aim for **6–12 titles** spanning decision makers, influencers, and end users — i.e. the people you'd actually want to email. Also derive **one fallback keyword** (single string only — see CLI quirks under Outreach · Research).

Show this to the user:
```
Contact search targets — <source>
  Titles:    <comma-separated list>
  Keyword:   <one fallback keyword>
  Rationale: <one sentence — why these map to the buying committee>
```
Ask: *"Approve, or refine?"*

### Setup · Signals
Produce up to 5 signals — *not 6, not 10*. Five is the cap. Distribute across categories so all three are represented:
- 1–2 **fit** signals (the most decisive profile constraints)
- 1–2 **potential** signals (strongest inverse-pain indicators)
- 1–2 **urgency** signals (always time-bounded)

**Bias toward `boolean` and `list`** when the answer space is enumerable. Use `open_text` only for compound per-item answers (e.g. "for each new hire, return name + title + start date + LinkedIn"). Frame `open_text` questions with explicit per-item field names so the response is parseable narrative.

For each signal, write:
```
Signal N — <category>
  Question:    <exact question — describe WHAT to find, not WHERE to look>
  AnswerType:  <one of: boolean, number, open_text, list, url, percentage, currency>
  Weight:      <1|2|3>     (max two 3s)
  Interpretation: <one line — e.g. "modes contains usage_based|credit_based|outcome_based">
```

**Question hygiene:** never tell Saber where to look. No "look at the pricing page", no "check LinkedIn", no "search BuiltWith / G2 / Sales Navigator". Saber picks sources internally and returns a `sources[]` array (snippet / title / url) plus `reasoning` (narrative explanation) in every response — surface those from the wrapper when verifying answers; never re-ask Saber to populate them inside the question.

It IS fine to enumerate the *answer space* in the question (e.g. "Stripe Billing, Chargebee, Recurly, Zuora, Maxio, homegrown") — that constrains the answer, not the source. It's also fine to ask for entity-URL fields (e.g. `linkedinUrl` of a hire, `jobUrl` of an open role) inside an `open_text` per-item template — those are structured facts about the entity that the SDR will use directly.

Print all five. Ask: *"Approve, or refine?"* Iterate until confirmed.

### Setup · Save
Write the config JSON to `~/.saber-skills/outreach-workflow/configs/<source>.json`. Create the `~/.saber-skills/outreach-workflow/configs/` directory if it doesn't exist.

Confirm: *"Saved. Future runs will skip Setup unless you say 'redefine'."*

Continue automatically into Outreach for the first prospect.

---

## Outreach — research one prospect and write to them

### Outreach · Prospect
Ask: *"Which prospect should we research and write outreach for? (one domain)"*

### Outreach · Research
Saber's contact search needs the company's LinkedIn URL. Fire it as a `url`-typed signal in parallel with the rest. Every call uses the CLI — no API keys, no environment variables, no project-local files:

```bash
# 1. Resolve LinkedIn URL (cheap, fast, used by contact search below)
saber signal --yes --domain <prospect> \
  --question "What is this company's official LinkedIn company page URL?" \
  --answer-type url --json &

# 2. For each signal in config.signals (all of them):
saber signal --yes --domain <prospect> \
  --question "<signal.question>" \
  --answer-type <signal.answerType> \
  --json &
```

Tell the user: *"Running [N+1] signals against `<prospect>` in parallel — expect results in ~30–60 seconds."*

Once the LinkedIn URL signal completes, fire the contact search using the saved `contactSearch.titles`. Note the CLI quirks: `--keyword` is **single-valued** (not repeatable) and there is **no `--limit` flag**. Default to **no keyword** (titles alone are usually specific enough); fall back to the saved keyword only if the title-only search returns zero.

```bash
saber contact search \
  --company-linkedin "<resolved-linkedin-url>" \
  --title "<title-1>" --title "<title-2>" ... \
  --json
```

### Outreach · Results
Show two tight blocks inline:

**Signals:**
```
Signals for <prospect>

# | Cat       | Question (short)         | Answer (key fields)              | Conf
1 | fit       | Pricing model            | isHybrid=true, modes=[usage,sub] | 0.95
2 | potential | Billing system           | stripe_billing, isOutgrown=true  | 0.9
3 | potential | AI monetization          | hasAIPricing=true, per_token     | 0.85
4 | urgency   | Finance leadership chg   | Jane Doe, CFO, started 2026-01   | 0.9
5 | urgency   | Billing/RevOps job posts | RevOps Lead (2026-03), Pricing PM| 1.0
```

**Contacts (top 5–10 from the search):**
```
Buying committee at <prospect>

Name              | Title                    | Department  | LinkedIn
Jane Doe          | Chief Financial Officer  | Finance     | linkedin.com/in/...
Alex Lee          | VP Engineering           | Engineering | linkedin.com/in/...
Sam Park          | Head of RevOps           | GTM         | linkedin.com/in/...
...
```

If a signal returned no match or low confidence, mark it `—` and don't fabricate. Never invent details that aren't in the result.

### Outreach · Write
Pick the **single best contact** to write to. Default heuristic: the highest-ranking decision-maker whose department aligns with the strongest signal hit. Show the user *"Writing to: [Name, Title]. Override? Or proceed?"*

Once chosen, output **two messages** in chat — no files, no CSV:

```
═══ <Prospect Company> · <Contact Name>, <Title> ═══

Cold email
  Subject: <subject>
  <2–4 short paragraphs, ≤120 words total>

LinkedIn connection note
  <≤300 chars>
```

**Personalization rules — strict:**

1. The opening line MUST reference at least one specific signal hit by exact detail. Not "I noticed you're growing" — anchor on a concrete fact (a job-post quote, a forum thread, a hire's name + start date, the exact billing tool they use).
2. The body MUST tie that signal to a specific pain point from the user's ICP — explain *why* the signal matters for the contact's role, not just that it exists.
3. Pick **one** signal as the hook. Two facts max in the body. More looks like a creep.
4. Use the contact's exact title and department to angle the message — what a CFO cares about ≠ what a VP Eng cares about, even with the same underlying signal.
5. Cite source URLs from the Saber `sources[]` wrapper internally for accuracy, but do not include URLs in the message.
6. If the signal results are weak (most low-confidence or empty), say so honestly: *"signals were thin for this prospect — here's a best-effort message, treat it as a draft."*

**Voice rules — strip the SDR/AI tells:**

The personalization research shouldn't read as a template. Apply this pass *after* drafting and before delivering.

- **Em dashes are an AI fingerprint.** Replace with periods, commas, or parens. One per message at most.
- **No "Saw the [X] role describe…"** — that's the recognizable scraped-LinkedIn opener. Use a *reaction* instead: *"…was a hell of a read"*, *"heroic ask"*, *"brutal"*, *"special kind of pain"*.
- **No "[Product] was built for exactly this shape"** — corporate brochure. Use first-person: *"I'm at X. We handle that exact shape…"*
- **Founder credentials get one short clause, no flourish.** *"Team came out of Adyen, built the internal billing there"* — not *"Our founders previously scaled Adyen's internal billing infrastructure, so the pain set is well-known."*
- **No "what we usually see right before X"** — generalized-expert positioning, very SDR.
- **No "Worth a quick comparison vs. continuing to extend Y?"** — CTA-shaped question. End on a real question that doesn't ask for a meeting: *"Curious whether the plan is to keep evolving in-house or swap the substrate."*
- **Break the four-paragraph pitch arc** (observation → product → credential → CTA). Combine paragraphs, lead with the question, or end on a fragment.
- **Lowercase or half-cased subjects** — *"the billing role"*, *"billing while doubling"*, *"your billing surface"*. Title Case Subject Lines Read As Sequence Templates.
- **Fragmented sentences are good.** *"Agent compute, student grants, enterprise contracts, fraud."* Period. People punctuate awkwardly when emailing.
- **First-person singular.** *"I'm at <your company>"* not *"We're <your company>"*.

The personalization stays. What changes is the prosody.

After printing, ask: *"Run another prospect, write to a different contact at this one, or done?"*

---

## Stop conditions and graceful exits

- **No CLI auth:** if `saber` errors with auth, tell the user to run `saber auth login` and stop.
- **CLI not installed:** if `saber --help` fails, tell the user to install the Saber CLI from [saber.app](https://saber.app) and stop. The skill has no fallback — Saber is a hard dependency.
- **LinkedIn URL signal returned nothing:** skip contact search, continue with signals only, flag in the output that you couldn't identify named contacts.
- **Saved config corrupt or missing fields:** show the file, ask whether to redefine (run Setup) or repair the JSON manually.
- **All signals failed in Outreach · Research:** print the errors, don't write outreach — bad data → bad message.
- **User says "refine signals" mid-run:** jump back to Setup · Signals with the existing ICP, regenerate signals, re-save.

---

## Notes

- **Five signals is a hard cap.** If you find yourself wanting six, drop the weakest. The whole point is focus.
- **Bias toward `boolean` and `list`** for enumerable answers. Reach for `open_text` only when each item has multiple fields (name + title + date + URL).
- **Single source of truth:** the saved config JSON is canonical. Don't duplicate ICP details elsewhere in conversation memory.
- **No Saber subscriptions, no list creation, no scoring, no batching.** All of that lives in `prospecting-workflow`.
- **No Anthropic SDK calls, no API keys, no `.env.local`, no project-local files.** The skill must run for any user who has the Saber CLI installed and authenticated. The only filesystem footprint is `~/.saber-skills/outreach-workflow/configs/<source>.json`.
- **Always pass `--yes`** on `saber signal` to skip the credit prompt. Each signal costs 2 credits; 5 signals + 1 LinkedIn-URL resolution = ~12 credits per prospect (contact search itself does not charge per-signal credits).
