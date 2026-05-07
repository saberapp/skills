# Saber Skills

Saber Skills is an open-source GTM skills library built by [Saber](https://saber.app). It gives revenue and GTM teams 28 skills covering the full outbound motion — from market mapping and ICP extraction to native fit + urgency scoring, personalised outreach, pipeline review, and end-to-end guided workflows.

Works with Claude Code, Cursor, Gemini CLI, and any agent that supports skills. Most skills work without any special tools. Skills that benefit from the [Saber CLI](https://saber.app) will say so — and every one of them offers a meaningful path without it.

---

## How it works

Saber Skills covers three stages of the GTM motion:

```
1. Understand  →  Who is your market? Who is your buyer? What signals matter?
2. Activate    →  Build lists. Run signals. Score and prioritise accounts.
3. Engage      →  Research accounts. Write outreach. Manage deals. Sync to CRM.
```

Each skill is a focused prompt that guides your agent through a structured workflow. Skills hand off to each other naturally — `signal-discovery` feeds `build-account-list`, which feeds `score-accounts`, which feeds `write-outreach`.

---

## Installation

### Via skills.sh (any agent)

```
npx skills add saberapp/skills
```

Works with Claude Code, Cursor, Gemini CLI, GitHub Copilot, and [40+ other agents](https://skills.sh).

### Via the Saber Marketplace (Claude Code)

```
/plugin marketplace add saberapp/saber-marketplace
/plugin install saber-skills@saber-marketplace
```

Saber Skills activates on the next session start. All available skills are listed in the session header.

### Direct URL install (Claude Code — contributors and testers)

```
/plugin install --url https://github.com/saberapp/skills
```

---

## Skills

### Signal foundations

| Skill | What it does |
|---|---|
| `signal-discovery` | Guided entry point — loads org context, runs `extract-icp` then `generate-signals` in sequence. Start here. |
| `extract-icp` | Researches a company domain and extracts a structured ICP: target profile, buying committee, pain points, buying triggers. |
| `generate-signals` | Turns a structured ICP into 12–15 weighted research signals with scoring rules across `icp_fit`, `urgency`, and `buying_signal` categories. |

### Guided workflows

| Skill | What it does |
|---|---|
| `prospecting-workflow` | End-to-end: build a Saber list, run signals across every company, find named contacts, export a combined CSV. Use this when you want one signed-off CSV instead of chaining `build-account-list` → `create-company-signals` → `build-contact-list`. |
| `outreach-workflow` | End-to-end per prospect: run saved signals, find named contacts, write a hyper-personalised cold email and LinkedIn DM. Setup once per source company; every subsequent run is one prospect. |

### Market and persona intelligence

| Skill | What it does |
|---|---|
| `market-map` | Maps a target market — segments, company density, key players, adjacent verticals, and recommended entry points. |
| `persona-research` | Builds a deep profile of a buying persona: motivations, challenges, buying behaviour, language they use, where to find them. |
| `competitive-intel` | Maps the competitive landscape, profiles key competitors, and builds battle cards for deals where they come up. |

### List building

| Skill | What it does |
|---|---|
| `build-account-list` | Builds a target account list using Saber, Apollo, HubSpot, or manual research. Runs signals if Saber is available. |
| `build-contact-list` | Builds a target contact list using Saber, Apollo, LinkedIn, or manual research. Runs signals if Saber is available. |
| `enrich-contacts` | Finds email and phone for contacts using Apollo, Cognism, Hunter.io, or any connected enrichment provider. |

### Research and qualification

| Skill | What it does |
|---|---|
| `research-account` | Builds a full account brief — signals, hiring, funding, news, tech stack, and a call prep summary. |
| `qualify-inbound` | Qualifies an inbound lead from native fit + urgency scores when Saber is available; falls back to manual scoring otherwise. |

### Scoring and prioritisation

| Skill | What it does |
|---|---|
| `extract-signal-templates` | Clusters historical ad-hoc signals into reusable templates so they can be referenced by scoring rules. One-shot migration. |
| `configure-scoring` | Sets up native scoring — profile, rules, assignments. Bridges the weighted model from `generate-signals` into the platform. |
| `manage-scoring` | Inspects and tunes scoring — list profiles and rules, edit point values, manage assignments, recompute, debug score contributions. |
| `score-accounts` | Ranks a list by current fit + urgency scores via the Saber CLI. Falls back to client-side weighted scoring for Apollo, HubSpot, or pasted data. |

### Signal activation

| Skill | What it does |
|---|---|
| `create-company-signals` | Activates company-level signal tracking via Saber subscriptions. Without CLI, outputs a research playbook. |
| `create-contact-signals` | Activates contact-level signal tracking via Saber subscriptions. |
| `manage-signals` | Lists all signal subscriptions — pause, resume, trigger, or review results. |

### Outreach

| Skill | What it does |
|---|---|
| `write-outreach` | Writes personalised cold email and LinkedIn messages. Pulls signal results from Saber if available; works from any signal data. |
| `build-sequence` | Designs a multi-step outreach sequence with signal-triggered message variants across email and LinkedIn. |
| `objection-handler` | Builds an objection library for your ICP — common objections mapped to the real concern, with response frameworks. |

### Pipeline and deal management

| Skill | What it does |
|---|---|
| `deal-coaching` | Analyses a specific deal — health assessment, risk flags, stakeholder map, and recommended next steps. |
| `pipeline-review` | Audits pipeline health from HubSpot or pasted data — at-risk deals, forecast analysis, and a weekly focus list. |
| `find-expansion-accounts` | Runs expansion signals against existing customers to surface upsell and cross-sell opportunities. |

### CRM

| Skill | What it does |
|---|---|
| `import-from-hubspot` | Pulls a HubSpot company list, view, or segment into Saber as a target account or contact list. |
| `sync-to-hubspot` | Pushes Saber signal results back to HubSpot as contact or company properties. |

---

## Example: from ICP to prioritised pipeline

```
You:     signal-discovery

Saber: Loading org context...
         Running extract-icp on saber.app...

         ICP — saber.app
         TARGET COMPANY: 25–500 employees, Series A–C
         PAIN POINTS: No real-time signal layer, disconnected sales stack
         BUYING TRIGGERS: New VP Sales hired, funding round, CRM migration

         Generating signals...
         14 signals across icp_fit / urgency / buying_signal — confirmed.

You:     configure-scoring

Saber: Translating the 14-signal model into a native scoring profile...
         Profile: ICP scoring (company)
         Rules: 9 in fit, 5 in urgency
         Bulk-assigned to 340 accounts on 'EMEA SaaS'.
         Auto-trigger is on — scores will recompute as signals fire.

You:     build-account-list

Saber: No Saber CLI detected. What prospecting tools do you have available?
         → User: Apollo

         Searching Apollo: B2B SaaS, 25–500 employees, DACH...
         Found 340 matching companies. Exported to list.

You:     score-accounts

Saber: Reading native scores for 340 accounts (ICP scoring profile)...

         High priority (combined ≥ 70): 12 accounts
         (Δ shows score change since the previous compute.)
         1. Kombo — fit 86 / urgency 82 (Δ +8) — New CTO, hiring 6 SDRs
         2. Liro  — fit 78 / urgency 80 (Δ +4) — Series B 3 months ago, HubSpot migration

You:     write-outreach   [for Kombo]

Saber: Subject: saw you're scaling the sales team at Kombo

         Hi [Name], noticed Kombo brought on a new CTO last month and is
         actively hiring across the sales team — usually means the GTM motion
         is being rebuilt from scratch...
```

---

## The Saber CLI

Most skills work without the Saber CLI. The CLI unlocks:

- **Automated signal runs** — run research questions across hundreds of accounts on a schedule
- **Contact-level signals** — research individuals by LinkedIn URL
- **List management** — create, filter, and import company and contact lists at scale
- **Credit-based pricing** — only pay for what you run

Install the CLI and connect it to your Saber account at [saber.app](https://saber.app). Run `saber init-claude` in your project to configure CLAUDE.md with your org context.

---

## Contributing a skill

1. Create a directory under `skills/` named after your skill
2. Add a `SKILL.md` with YAML frontmatter and the skill prompt:

```markdown
---
name: my-skill
description: One-line description shown in the session header.
---

# My Skill

What Claude should do when this skill is invoked. Skills should work without
the Saber CLI where possible and offer a meaningful fallback when they can't.
```

3. Open a pull request — skills are picked up automatically from the `skills/` directory.

See any existing skill in `skills/` for reference.

---

## License

MIT — see [LICENSE](./LICENSE)
