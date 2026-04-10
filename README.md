# Saber Arsenal

Saber Arsenal is Saber's open-source GTM and RevOps skills library for Claude Code, inspired by [obra/superpowers](https://github.com/obra/superpowers). It packages reusable sales and revenue intelligence skills that plug directly into Claude Code via the plugin system. Many skills are powered by the [Saber CLI](https://saber.app) — each skill will check for the CLI and offer a fallback path if it is not installed.

## Installation

### Marketplace install (recommended)

Run `/plugins` to open the plugin manager, then add `saberapp/arsenal-marketplace` as a marketplace. This installs `saber-arsenal` automatically.

### Direct URL install (contributors and testers)

```
/plugins install --url https://github.com/saberapp/arsenal
```

## Available skills

| Skill | Description | Requires Saber CLI |
|---|---|---|
| `signal-discovery` | Guided entry point — runs `extract-icp` then `generate-signals` in sequence. Start here. | Optional |
| `extract-icp` | Research a company domain and extract a structured ICP: target profile, pain points, buying triggers. | No |
| `generate-signals` | Turn a structured ICP into 12–15 weighted research signals with scoring rules. | No |
| `create-company-signals` | Activate company-level signal tracking. | Yes |
| `create-contact-signals` | Activate contact-level signal tracking. | Yes |
| `build-account-list` | Build a target account list and run company signals. | Yes |
| `build-contact-list` | Build a target contact list and run contact signals. | Yes |
| `enrich-contacts` | Find email and phone for contacts using Apollo, Cognism, Hunter.io, or any available MCP/CLI enrichment provider. | No |
| `research-account` | Build a full account brief — signals, web research, CRM data, and a call prep summary. | Optional |
| `qualify-inbound` | Score an inbound lead using Saber signals and return a High / Medium / Low rating. | Optional |
| `score-accounts` | Rank a list of accounts by combined signal strength using a weighted scoring model. | Yes |
| `write-outreach` | Write personalised cold email and LinkedIn messages using Saber signal results. | Optional |
| `build-sequence` | Design a multi-step outreach sequence with signal-triggered message variants. | Optional |
| `find-expansion-accounts` | Identify existing customers showing upsell or cross-sell signals. | Optional |
| `manage-signals` | View, pause, resume, and trigger active signal subscriptions. | Yes |
| `import-from-hubspot` | Pull a HubSpot list into Saber as a target account or contact list. | Yes |
| `sync-to-hubspot` | Push Saber signal results back to HubSpot as contact or company properties. | Yes |

## Contributing a new skill

1. Create a directory under `skills/` named after your skill (e.g. `skills/my-skill/`)
2. Add a `SKILL.md` file inside that directory with YAML frontmatter and prompt content:

```markdown
---
name: my-skill
description: One-line description of what this skill does.
---

# My Skill

Prompt content goes here. Describe what Claude should do when this skill is invoked.
If the skill uses the Saber CLI, include a "Saber CLI check" section that handles the
case where the CLI is not installed.
```

3. Open a pull request — skills are automatically picked up from the `skills/` directory.

## License

MIT — see [LICENSE](./LICENSE)
