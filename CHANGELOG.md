# Changelog

All notable changes to this project will be documented in this file.

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
