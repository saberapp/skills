# Saber Arsenal

Saber Arsenal is the open-source GTM skills library for Claude Code, built by [Saber](https://saber.app). It brings the full outbound motion — ICP extraction, signal generation, account scoring, personalised outreach, and CRM sync — into 17 slash commands that run directly inside your AI agent.

Skills are powered by the [Saber CLI](https://saber.app). If the CLI isn't installed, each skill checks for it and offers a fallback path.

---

## How it works

The Saber workflow follows three stages. Arsenal has skills for each one.

```
1. Define   →  Who is your ICP? What signals indicate buying intent?
2. Activate →  Build lists. Run signals. Score and prioritise accounts.
3. Engage   →  Research accounts. Write personalised outreach. Sync to CRM.
```

Most GTM teams do this manually — stitching together LinkedIn, news alerts, spreadsheets, and intuition. Arsenal automates each step and connects them together. You end up with a prioritised list of accounts and personalised messages that reference the specific signals that fired.

---

## Quick start

1. Install Arsenal (see below)
2. Run `signal-discovery` — define your ICP and generate a scored signal set
3. Run `build-account-list` — create a target list and activate signals
4. Run `score-accounts` after signals complete — surface your highest-intent accounts
5. Run `write-outreach` — draft personalised messages that reference the signals that fired

---

## Installation

### Via the Saber Marketplace (recommended)

In Claude Code:

```
/plugin marketplace add saberapp/saber-marketplace
/plugin install saber-arsenal@saber-marketplace
```

Arsenal activates on the next session start. You'll see all available skills listed in the session header.

### Direct URL install (contributors and testers)

```
/plugin install --url https://github.com/saberapp/arsenal
```

---

## Workflows

### Signal foundations — start here

| Skill | What it does |
|---|---|
| `signal-discovery` | **Guided entry point.** Loads your org context, runs `extract-icp` then `generate-signals` in sequence. Start here if you're setting up signals for the first time. |
| `extract-icp` | Researches a company domain and produces a structured ICP: target company profile, buying committee, pain points, buying triggers, and existing customers. |
| `generate-signals` | Turns a structured ICP into 12–15 weighted research signals across three categories — `icp_fit`, `urgency`, and `buying_signal` — with scoring rules ready for `score-accounts`. |

### List building

| Skill | What it does |
|---|---|
| `build-account-list` | Creates a Saber company list with filters (industry, size, geography, tech stack) and activates signal subscriptions against it. |
| `build-contact-list` | Creates a Saber contact list from LinkedIn URLs and activates contact-level signal tracking. |
| `enrich-contacts` | Finds email and phone for contacts in a Saber list using Apollo, Cognism, Hunter.io, or any connected MCP/CLI enrichment provider. |

### Research and qualification

| Skill | What it does |
|---|---|
| `research-account` | Builds a full account brief for a single company — signals, hiring, funding, news, tech stack, and a call prep summary. |
| `qualify-inbound` | Scores an inbound lead by running your signals against their domain. Returns a High / Medium / Low rating with reasoning. |
| `score-accounts` | Ranks all companies in a Saber list by signal strength using the weighted scoring model from `generate-signals`. Surfaces your highest-priority accounts. |

### Outreach

| Skill | What it does |
|---|---|
| `write-outreach` | Writes personalised cold email and LinkedIn messages. Pulls signal results from Saber to reference specific events — funding, hiring, leadership changes — as the reason for reaching out. |
| `build-sequence` | Designs a multi-step outreach sequence (email + LinkedIn) with signal-triggered message variants. Each touchpoint uses a different signal angle. |
| `find-expansion-accounts` | Runs expansion and upsell signals against your existing customer base to surface accounts ready to buy more. |

### Signal operations

| Skill | What it does |
|---|---|
| `manage-signals` | Lists all active signal subscriptions. Pause, resume, trigger, or review results across your full signal library. |

### CRM integration

| Skill | What it does |
|---|---|
| `import-from-hubspot` | Pulls a HubSpot company list, view, or segment into Saber as a target account or contact list — via HubSpot MCP or CSV export. |
| `sync-to-hubspot` | Pushes Saber signal results back to HubSpot as contact or company properties, so reps can see intent data without leaving their CRM. |

---

## Example: from ICP to prioritised pipeline

```
You:     signal-discovery

Arsenal: Loading org context from saber org get...
         Running extract-icp on saber.app...

         ICP — saber.app
         TARGET COMPANY: 25–500 employees, Series A–C
         PAIN POINTS: No real-time signal layer, disconnected sales stack,
                      outreach based on assumptions
         BUYING TRIGGERS: New VP Sales hired, funding round, CRM migration

         Generating signals...

         Signal 1 — icp_fit    Is this a B2B company?          weight: 3, disqualifier
         Signal 2 — icp_fit    Does the company have an SDR function?  weight: 3
         Signal 6 — urgency    New VP Sales / CRO hired in last 6 months?  weight: 3
         Signal 10 — buying_signal  Does the company lack a RevOps function?  weight: 2
         [14 signals total]

You:     build-account-list

Arsenal: Created list "Series A–C B2B SaaS – DACH" (482 companies)
         Activating 14 signal subscriptions...
         Signals will run weekly. Use manage-signals to check progress.

You:     score-accounts   [after signals complete]

Arsenal: ## Account Scores

         High fit (70+)
         1. Kombo       – score 84  – New CTO hired, hiring 6 SDRs, no RevOps
         2. Liro        – score 79  – Series B 3 months ago, HubSpot migration
         3. Basewell    – score 71  – 5 sales tools, no signal layer, hiring BDRs

You:     write-outreach   [for Kombo]

Arsenal: Subject: saw you're scaling the sales team at Kombo

         Hi [Name], noticed Kombo brought on a new CTO last month and is
         hiring across the sales team. Most GTM teams at this stage are
         dealing with the same thing: reps spending half their time on
         manual research instead of selling...
```

---

## All skills

| Skill | Requires Saber CLI |
|---|---|
| `signal-discovery` | Optional |
| `extract-icp` | No |
| `generate-signals` | No |
| `build-account-list` | Yes |
| `build-contact-list` | Yes |
| `enrich-contacts` | No |
| `research-account` | Optional |
| `qualify-inbound` | Optional |
| `score-accounts` | Yes |
| `write-outreach` | Optional |
| `build-sequence` | Optional |
| `find-expansion-accounts` | Optional |
| `manage-signals` | Yes |
| `import-from-hubspot` | Yes |
| `sync-to-hubspot` | Yes |

Skills marked **Optional** use the Saber CLI when available and degrade gracefully when it isn't. Skills marked **No** work entirely without the CLI.

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

What Claude should do when this skill is invoked. If the skill uses the Saber CLI,
include a "Saber CLI check" section that handles the case where it isn't installed.
```

3. Open a pull request — skills are automatically picked up from the `skills/` directory.

See any existing skill in `skills/` for reference.

---

## Requirements

- [Claude Code](https://claude.ai/code)
- A [Saber account](https://saber.app) and the Saber CLI (for CLI-dependent skills)

---

## License

MIT — see [LICENSE](./LICENSE)
