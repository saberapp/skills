# Changelog

All notable changes to this project will be documented in this file.

## [0.1.0] - 2026-04-10

### Added

- Initial scaffolding of the Arsenal plugin
- `hello-world` placeholder skill confirming the plugin loads correctly and serving as a template for new GTM skills
- Claude Code plugin manifest (`.claude-plugin/plugin.json`) with skill and hook registration
- Marketplace descriptor (`.claude-plugin/marketplace.json`) for distribution via the Claude Code plugin marketplace
- `SessionStart` hook (`hooks/session-start`) that announces Arsenal and lists available skills at the start of each session
- Version management config (`.version-bump.json`) to keep `package.json`, `plugin.json`, and `marketplace.json` in sync across releases
