# Saber Arsenal

Saber Arsenal is Saber's open-source GTM and RevOps skills library for Claude Code, inspired by [obra/superpowers](https://github.com/obra/superpowers). It packages reusable sales and revenue intelligence skills that plug directly into Claude Code via the plugin system. All skills are powered by the [Saber CLI](https://saber.app) for account intelligence, signal tracking, and prospecting.

## Installation

### Marketplace install (recommended)

```
/plugins add-marketplace saberapp/arsenal-marketplace
/plugins install saber-arsenal
```

### Direct URL install (contributors and testers)

```
/plugins install --url https://github.com/saberapp/arsenal
```

## Available skills

| Skill | Description |
|---|---|
| `saber-signal-discovery` | Define buying signals that match your ICP — start here before creating signals or building lists. |
| `saber-create-company-signals` | Activate company-level signal tracking using the Saber CLI. |
| `saber-create-contact-signals` | Activate contact-level signal tracking using the Saber CLI. |
| `saber-build-account-list` | Build a target account list using the Saber CLI and run company signals against it. |
| `saber-build-contact-list` | Build a target contact list using the Saber CLI and run contact signals against it. |

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
All skills should use the Saber CLI where relevant.
```

3. Open a pull request — skills are automatically picked up from the `skills/` directory.

## License

MIT — see [LICENSE](./LICENSE)
