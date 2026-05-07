# Changelog

All notable changes to this project will be documented in this file.

## [0.3.0] - 2026-05-07

### Added

- `Guided workflows` category in the README and `SessionStart` hook — end-to-end alternatives to chaining atomic skills.
- `prospecting-workflow` skill — guided multi-stage workflow (filter → list → signals → contacts → CSV) with a credit-discipline guard and pre-trigger safeguards. Use this when you want one signed-off CSV instead of running `build-account-list` → `create-company-signals` → `build-contact-list` separately.
- `outreach-workflow` skill — per-prospect guided workflow (signals → contact search → cold email + LinkedIn DM). One-time setup defines the source-company ICP, contact-search targets, and ≤5 weighted signals; every subsequent run reuses them. Configs persist at `~/.saber-skills/outreach-workflow/configs/<source>.json`.

## [0.2.0] - 2026-05-05

### Added

- `extract-signal-templates` skill — one-shot migration that clusters historical ad-hoc signal executions into reusable templates so they become scoreable. Wraps `saber template extract propose | apply`.
- `configure-scoring` skill — set up native scoring profiles, rules, and assignments. Bridges the weighted model from `generate-signals` into the platform.
- `manage-scoring` skill — inspect and tune profiles, edit point values, manage assignments, recompute, and debug score contributions.
- `skills/_shared/scoring.md` — shared reference doc for scoring concepts (dimensions, profiles, rules, assignments, point-value shapes, auto-trigger behaviour) referenced by every scoring-aware skill.
- `Scoring and prioritisation` category in the README and `SessionStart` hook.

### Changed

- `score-accounts` — Path A now reads native fit + urgency scores via `saber scoring scores` instead of computing client-side. Path B retains the weighted model fallback for non-Saber data.
- `qualify-inbound` — surfaces native fit + urgency separately with a tier matrix (Hot / Right fit, slow timing / Reactive / Possible fit / Not a fit) when scoring is configured. Manual qualification preserved as a fallback.
- `find-expansion-accounts` — uses a dedicated expansion scoring profile (growth / problem / intent / at-risk → fit + urgency mapping) and reads native scores; manual fallback retained.
- `build-account-list` — adds an optional bulk-assign step after list creation so signals flowing into the list also flow into scores.
- `signal-discovery` and `generate-signals` — hand off to `configure-scoring` to materialize the weighted model as native scoring rules.
- `manage-signals` — cross-links to `manage-scoring` and notes that scores auto-recompute when signals complete.

## [0.1.0] - 2026-04-10

### Added

- Initial scaffolding of the Saber Arsenal plugin
- `saber-signal-discovery` skill — define buying signals matching your ICP
- `saber-create-company-signals` skill — activate company-level signal tracking via the Saber CLI
- `saber-create-contact-signals` skill — activate contact-level signal tracking via the Saber CLI
- `saber-build-account-list` skill — build target account lists and run company signals
- `saber-build-contact-list` skill — build target contact lists and run contact signals
- Claude Code plugin manifest (`.claude-plugin/plugin.json`) with skill and hook registration
- Marketplace descriptor (`.claude-plugin/marketplace.json`) for distribution via the Claude Code plugin marketplace
- `SessionStart` hook (`hooks/session-start`) that announces Saber Arsenal and lists available skills at the start of each session
- Version management config (`.version-bump.json`) to keep `package.json`, `plugin.json`, and `marketplace.json` in sync across releases
